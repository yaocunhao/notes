# 二、linestring 的一些初始化方法

- push_back
- bg::append
-  polyline my_polyline{}

```c++
#include <boost/geometry.hpp>
#include <boost/geometry/geometries/register/linestring.hpp>

namespace bg = boost::geometry;

// 定义点类型
typedef bg::model::d2::point_xy<double> point_2d;

// 定义折线类型
typedef bg::model::linestring<point_2d> polyline;

int main() {
    // 创建一个空的折线
    polyline empty_polyline;

    // 创建并初始化一个包含三个点的折线
    polyline my_polyline;
    my_polyline.push_back(point_2d(0.0, 0.0));
    my_polyline.push_back(point_2d(1.0, 1.0));
    my_polyline.push_back(point_2d(2.0, 2.0));

    return 0;
}

#include <boost/geometry.hpp>
#include <boost/geometry/geometries/register/linestring.hpp>

namespace bg = boost::geometry;

// 定义点类型
typedef bg::model::d2::point_xy<double> point_2d;

// 定义折线类型
typedef bg::model::linestring<point_2d> polyline;

int main() {
    // 使用 Boost.Geometry 算法创建并初始化一个包含三个点的折线
    polyline my_polyline;
    bg::append(my_polyline, point_2d(0.0, 0.0));
    bg::append(my_polyline, point_2d(1.0, 1.0));
    bg::append(my_polyline, point_2d(2.0, 2.0));

    return 0;
}


#include <boost/geometry.hpp>
#include <boost/geometry/geometries/register/linestring.hpp>

namespace bg = boost::geometry;

// 定义点类型
typedef bg::model::d2::point_xy<double> point_2d;

// 定义折线类型
typedef bg::model::linestring<point_2d> polyline;

int main() {
    // 使用范围初始化创建折线
    polyline my_polyline {
        point_2d(0.0, 0.0),
        point_2d(1.0, 1.0),
        point_2d(2.0, 2.0)
    };

    return 0;
}

```

