装饰器：
```
@workflow
def install(ctx, **kwargs):
     ""Default install workflow"""
    lifecycle.install_node_instances(
        graph=ctx.graph_mode(),
        node_instances=set(ctx.node_instances))
```


装饰器：先执行workflow（参数使install函数），然后执行他内部的函数，最后将@workflow执行完成后在执行install函数

