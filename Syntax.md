"key[]":{}                                         // 查询数组	return a arrary named 'key'
"key{}":[1,2,3]                                    // 匹配选项范围	matching 'key' is among [1,2,3]
"key{}":"<=10;length(key)>1..."                    // 匹配条件范围	matching 'key' satisfy condition
"key()":"function(arg0,arg1...)"                   // 远程调用函数	remote function
"key@":"key0/key1.../targetKey"                    // 引用赋值	reference other fields in the same json
"key$":"%abc%"                                     // 模糊搜索	matching
"key~":"^[0-9]+$"                                  // 正则匹配	regex
"key%":"2018-01-01,2018-10-01"                     // 连续范围	continuous 
"key+":[1]                                         // 增加/扩展	
"key-":888.88                                      // 减少/去除	
"name:alias"                                       // 新建别名	
"@combine":"name~,tag~"                            // 条件组合	
"@column":"id,sex,name"                            // 返回字段	
"@group":"userId"                                  // 分组方式	
"@having":"max(id)>=100"                           // 聚合函数	
"@order":"date-,name+"                             // 排序方式	
"@schema":"sys"                                    // 集合空间	
"@database":"POSTGRESQL"                           // 跨数据库	
"@explain":true                                    // 性能分析	
"@role":"LOGIN"                                    // 访问角色	

