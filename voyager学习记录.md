# 一、字段含义

- 先看voyager之中onboard/hdmap/hdmap_protos/road.proto，查看输出的pb数据
- 再看model文件和数据库之中的文件

```protobuf
syntax = "proto3";

import "hdmap_protos/condition.proto";
import "hdmap_protos/geometry.proto";
import "hdmap_protos/track_system.proto";

package hdmap;

// Markings painted on pavement indicate lanes to use for travelling.
//铺在人行道上的标记表示行车路线。
message LaneMarking {
  // The order of points in this line is expected to be along the direction of
  // the lane.这条线上的点的顺序预计是沿着车道的方向
  Polyline line = 1;

  // Attributes of a lane marking.
  // 车道标记的属性
  message Attribute {
    enum Type {
      UNKNOWN_TYPE = 0;
      VIRTUAL = 1;
      BROKEN = 2;
      SOLID = 3;
      DOUBLE_BROKEN = 4;
      DOUBLE_SOLID = 5;
    }
    Type type = 1;

    // Start of this attribute.
    //attribute 属性的开始
    TrackSystem track = 2;
  }
  repeated Attribute attrs = 2;

  // Id of this lane marking.
  //车道标记的id
  int64 id = 3;
}

// A numerical limit with s-coordinate.
//坐标的数值极限
message Limit {
  float limit_max = 1;
  float limit_min = 2;
  TrackSystem track = 3;  // start of this limit 这个极限的开始
}

// Conflict lane is only available for the lanes in intersection. It contains
// lane id and the type of conflict.
//冲突车道仅适用于交叉口的车道。它包含车道id和冲突类型
message ConflictLane {
  int64 lane_id = 1;
  enum ConflictType {
    UNKNOWN_TYPE = 0;
    CONFLICT_LANE = 1;  // Conflict with this lane. 和这条车道冲突
    YIELD_LANE = 2;     // A conflict lane this lane needs to yield to. 冲突的车道，这条车道需要让路
  }
  ConflictType type = 2;
}

// Lane is defined as the drivable area between two lane markings.
// Next ID to use: 21
// 车道定义为两条车道标线之间的可行驶区域。下一个要使用的ID:21
message Lane {
  // Id of this lane.
  int64 id = 1;

  // Deprecated, use left_lane_marking_id and right_lane_marking_id instead.
  // Indices of left and right lane markings in the lane section.
  //不推荐使用，请使用左_lane _marking _id和右_lane _marking _id
  //车道段中左右车道标线的索引
  int32 left_lane_marking_index = 2 [ deprecated = true ];
  int32 right_lane_marking_index = 3 [ deprecated = true ];

  // Assistant line for planning. It may not be the geometric centerline.
  //计划助理线。它可能不是几何中心线
  Polyline assistant_line = 4;

  // Current condition of this lane.
  //这条车道的现状
  Condition condition = 5;

  enum LaneType {
    UNKNOWN_TYPE = 0;
    VIRTUAL = 1;  // Nonexist lanes. Added for convenience of planning. 不存在的车道。为方便规划而添加
    REGULAR = 2;
    BUS = 3;
    BIKE = 4;
    WAITING = 5;  // Waiting area for left turns.左转等候区
    // Used for turning left to exit this lane. There might be conflicting
    //用于左转离开这条车道。可能存在冲突
    // vehicles. Should not be used in planning.车辆。不应用于规划
    CENTER_LEFT_TURN = 6;
    EMERGENCY = 7;
  }
  LaneType type = 6;

  // Turn type at lane end. We use
  // bitmap(u_turn_bit|left_turn_bit|straight_bit|right_turn_bit) to describe
  // turn direction.
  //车道尽头的转弯类型。我们使用位图（u_turn_bit | left_turn_bit | right_turn_bit | right_turn_bit）来描述转弯方向
  enum Turn {
    UNKNOWN_TURN = 0;
    RIGHT = 1;
    STRAIGHT = 2;
    STRAIGHT_RIGHT = 3;
    LEFT = 4;
    LEFT_RIGHT = 5;
    LEFT_STRAIGHT = 6;
    LEFT_STRAIGHT_RIGHT = 7;
    U_TURN = 8;
    U_TURN_AND_STRAIGHT = 10;
    U_TURN_AND_LEFT = 12;
    U_TURN_AND_LEFT_AND_STRAIGHT = 14;
    ALL_TURNS = 15;
  }
  // It's displayed as |exit type| in the web map editor.
  //它在网络地图编辑器中显示为|退出类型|
  Turn turn = 7;

  repeated Limit speed_limits = 8;  // in meters per second.以米每秒为单位。

  repeated Limit height_limits = 9;  // in meters. 以米为单位

  // Ids of connected lanes.
  //连接车道的ID
  repeated int64 predecessors = 10;
  repeated int64 successors = 11;

  // Neighbor lanes.
  // NOTE(Tingran): This field is not released in map data.
  //邻居车道注释（Tingran）：此字段不在地图数据中发布
  int64 left_lane_id = 12;
  // NOTE(Tingran): This field is not released in map data.
  // 注（Tingran）：此字段不在地图数据中发布
  int64 right_lane_id = 13;

  // Signals associated with this lane.
  //与此车道相关的信号灯
  repeated int64 signal_ids = 14;

  // Ids of left and right lane markings in the lane section.
  // 车道段中左右车道标线的ID
  int64 left_lane_marking_id = 15;
  int64 right_lane_marking_id = 16;

  // Conflict lanes associated with this lane. It is only available in lanes
  // in an intersection.
  //与此车道关联的冲突车道。它只在十字路口的车道上可用
  repeated ConflictLane conflict_lanes = 17;

  // When driving in this lane, you need to turn on the left or right lights.
  // Most lanes are of unknown state and the correct values are only
  // available in lane changes.
  //在这条车道上行驶时，你需要打开左灯或右灯。大多数车道处于未知状态，正确的值仅在车道更改时可用
  enum TurnSignal {
    UNKNOWN_SIGNAL = 0;
    RIGHT_SIGNAL = 1;
    LEFT_SIGNAL = 2;
    STRAIGHT_SIGNAL = 3;
  }
  TurnSignal turn_signal = 18;

  // Watch line for left turn waiting area.
  // 左转等候区的警戒线
  Polyline watch_line = 19;
  repeated Limit recommended_speeds = 20;
}

// A lane section is a group of lanes with constant number of lanes in a road.
// 车道段是道路上车道数恒定的一组车道
message LaneSection {
  // Lane markings in this section.
  // 本节中的车道标记
  repeated LaneMarking lane_markings = 1;

  // Lanes from left to right, by facing the direction of this road.
  // 从左到右的车道，朝向这条路的方向
  repeated Lane lanes = 2;

  // Start of this section.
  // 本节开头
  TrackSystem track = 3;

  // Id of this lane section.
  // 此车道段的id
  int64 id = 4;

  // Road id for this lane section.
  // 此车道段的道路id
  int64 road_id = 5;

  // Left-most and right-most drivable lane markings, treated as the left and
  // right drivable borders of this section.
  // 最左侧和最右侧的可行驶车道标记，视为该路段的左侧和右侧可行驶边界
  int64 left_most_lane_marking_id = 6;
  int64 right_most_lane_marking_id = 7;
}

```

