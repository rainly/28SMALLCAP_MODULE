# SUMMARY
1. 模块化
2. 次级函数（utils函数除外）均以“_”开头
3. 默认开启性能分析
4. 调仓计数改为“4天”

# 代码结构
1. 引入外部模块
2. 主体函数
3. pick & filter
4. stop loss
5. trade
6. utils
7. log

# 主体函数
1. 包含全部预制函数，已注释掉未使用的函数
2. reset_day_param 方法在盘前调用
3. set 和 reset 方法

# PICK & FILTER
1. 选股仅依赖过滤器，筛选出 stock_list 后，使用 buy_stock_count 截取
2. 现有过滤器如下：
    filter_paused
    filter_st
    filter_gem
    filter_limitup
    filter_limitdown
    filter_by_growth_rate
    filter_blacklist
    filter_new
    filter_by_query
    filter_by_rank
3. 因为 query 查询数据库相对比较慢，目前把所有数据库查询筛选放在 filter_by_query 中
4. 过滤器会依照 _filter_register 顺序进行，先后顺序会影响速度， filter_by_rank 必须放在最后
5. 当前顺序为原版本顺序，为了回测速度进行妥协。实测效果，将 filter_by_query 放在评分前更好，采取全评分效果最佳，但速度非常慢

# STOP LOSS
1. 止盈和止损最终都会触发卖出操作，所以这里均以 stop_loss 指代
2. 止损器分为 day 和 minute 两种， day 级别方法会在每日调仓时间进行， minute 级别方法会每分钟调用
3. 指数止损器会触发全部平仓操作，依照原版本方式，暂停当天交易

# TRADE
1. 新增 _order 方法，买卖指定数量
2. 平仓时，使用 position.closeable_amount 代替 _order_target_value(security, 0)，提高成交成功率

# UTILS
1. 改进了 get_close_price 方法，获取前n日内的第一个close数据，以保证计算涨幅的相对准确
2. 新增 func_register 方法，注册过滤器、止损器等

# LOG
1. 新增 log_section 方法，记录日志小节
2. 新增 log_filter 和 log_stop_loss 方法，记录过滤器和止损器