## Backbone.Collection ##    
集合是一系列有序的模型。    
**extend** `Backbone.Collection.extend(properties, [classProperties])`   

**model** `collection.model`    

**constructor/initialize** `new Collection([models], [options])`   

**models** `collection.models`   

**toJSON** `collection.toJSON()`   

**sync** `collection.sync(method, collection, [options])`    

### Underscore Methods(28) ###    
Underscore.js提供28个迭代函数。   


**add** `collection.add(models, [options])`   
向集合添加一个模型或模型数组，会触发`"add"`事件。   

**remove** `collection.remove(models, [options])`  

**reset** `collection.reset([models], [options])`  

