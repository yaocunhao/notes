# 一、lane 车道处理逻辑分析

```python
class PermissionRequired:
  """
  默认情况下，当不满足任何权限时，它将引发PermissionDenied异常。
  """

# 进行一个参数的设置
  def __init__(self, perm):
    if isinstance(perm, six.string_types):  # 判断两个类型是否相等
      self._perms = (perm,)
    else:
      self._perms = perm

# 调用的是这个方法，返回了一个闭包函数
  def __call__(self, func):  # __call__可以函数名直接调用 -> 返回一个_wrapper对象
    def _wrapper(view, request, *args, **kwargs): #这个函数被返回，功能为验证请求和响应
      if not isinstance(request, HttpRequest):
        pass
      if request.user.has_perms(self._perms): #验证成功，返回一个处理函数
        return func(view, request, *args, **kwargs) #返回一个get(view,request,*args,**kwargs)
      
      raise PermissionDenied #否则报异常

    return _wrapper




@PermissionRequired('map_editor.get') # 实际实际返回的是wrapper函数，该函数的功能是
  def get(self, request):
    """Get lane by id.""" #通过id获取lane

    obj_id = get_request_value(request, 'id')
    lane = Lane.get_by_id(obj_id, True, True)
    if lane is None:
      return JsonResponseUtil.not_found('lane(%s) is not found' % obj_id)
    lane['lanemarkings'] = LanemarkingV2.get_dicts_by_ids(
        [lane['left_lane_marking_id'], lane['right_lane_marking_id']])
    return JsonResponseUtil.success(lane)

  @PermissionRequired('map_editor.post')
  def post(self, request, lane_id=None, operation=None):
    """Add or update lane.""" #增加或者更新车道
    try:
      if lane_id is None and operation is None:
        obj_data = self.add_lane(request)
      elif lane_id is None and operation is not None:
        raise ValueError("invalid lane id")
      else:
        obj_data = getattr(self, '_op_' + operation)(request, lane_id)

      if isinstance(obj_data, tuple):
        obj, alert_msg, extra_msg = obj_data
        return JsonResponseUtil.success(model_to_dict(obj), alertmsg=alert_msg,
                                        extramsg=extra_msg)
      elif isinstance(obj_data, JsonResponse):
        return obj_data

      extra_msg = obj_data
      return JsonResponseUtil.success(extramsg=extra_msg)

    except (ValueError, ObjectIsRelated) as err:
      return JsonResponseUtil.fail(msg=str(err))
    except ObjectDoesNotExist as err:
      return JsonResponseUtil.not_found(msg=str(err))

  @operation_log(Lane, 'create')
  def add_lane(self, request): #增加车道
    """Add lane."""
    params = LaneV2ParamParser.parse(request, SourceType.MAP_EDITOR)
    lane, close_lane_succeeded_pairs, close_lane_failed_pairs, extra_msg = Lane.create(**params)
    alert_msg = self._generate_alert_msg(close_lane_succeeded_pairs,
                                         close_lane_failed_pairs)
    return lane, alert_msg, extra_msg

  @classmethod
  def _generate_alert_msg(cls, succeeded_pairs, failed_pairs):
    """Generate the alert MSG.""" #生成警报MSG
    alert_msg = ''
    if succeeded_pairs:
      alert_msg += "Succeeded close_lane lane_marking pairs: %s;" % succeeded_pairs
    if failed_pairs:
      alert_msg += "Failed close_lane lane_marking pairs: %s;" % failed_pairs
    return alert_msg

  @validity_check(Lane)
  @operation_log(Lane, 'update')
  def _op_update(self, request, lane_id):
    """Update lane.""" #更新车道
    params = LaneV2ParamParser.parse(request, SourceType.MAP_EDITOR, lane_id)
    lane, close_lane_succeeded_pairs, close_lane_failed_pairs, extra_msg = Lane.update(
        lane_id, **params)
    alert_msg = self._generate_alert_msg(close_lane_succeeded_pairs,
                                         close_lane_failed_pairs)
    return lane, alert_msg, extra_msg

  @classmethod
  def _op_close(cls, request, lane_id):
    """Close lane."""
    closed_lane_ids = Lane.close(lane_id)
    lanes = Lane.get_dicts_by_ids(closed_lane_ids)
    for lane in lanes:
      lane['lanemarkings'] = LanemarkingV2.get_dicts_by_ids(
          [lane['left_lane_marking_id'], lane['right_lane_marking_id']])
    return JsonResponseUtil.success({'lanes': lanes})

  @classmethod
  @validity_check(Lane)
  @operation_log(Lane, 'delete')
  def _op_delete(cls, request, lane_id):
    """Delete lane.""" #进行删除
    extra_msg = Lane.remove(lane_id)
    return extra_msg
```

