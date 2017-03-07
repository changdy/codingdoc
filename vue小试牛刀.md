* html标签
	```html
	<tbody id = "unbundling_grid"><tr is="my-component" v-for="(value,index) in rowList" v-bind:data = "value" v-bind:date = "dateTimeArr[index]" v-bind:type-info = "typeInfoArr[index]"></tr></tbody>
	<template id="myComponent">
	    <tr>
	        <td>{{data.plateNumber}}</td>
	        <td>{{data.unbundlerId}}</td>
	        <td>{{data.nickname}}</td>
	        <td>{{data.mobile}}</td>
	        <td>{{date}}</td>
	        <td>{{data.memo}}</td>
	        <td>{{typeInfo}}</td>
	    </tr>
	</template>
	```
* 注册相关
	```javascript
	Vue.component('my-component', {
	            props: ['data','typeInfo','date'],
	            template: '#myComponent'
	        });
	        vm = new Vue({
	            el: '#unbundling_grid',
	            data: {
	                rowList:[]
	            },
	            computed: {
	                dateTimeArr: function () {
	                    var arr = new Array();
	                    for (var i = 0; i < this.rowList.length; i++) {
	                        if(this.rowList[i].unbundlingDate){
	                            arr.push('test');//伪代码
	                        }else{
	                            arr.push('');
	                        }
	                    }
	                    return arr;
	                },
	                typeInfoArr:function () {
	                    var arr = new Array();
	                    for (var i = 0; i < this.rowList.length; i++) {
	                        if(this.rowList[i].type){
	                            var type = this.rowList[i].type;
	                            if( type == 0){
	                                arr.push('默认');
	                            }else if(type == 1 ){
	                                arr.push('同意');
	                            }
	                        }else{
	                            arr.push('');
	                        }
	                    }
	                    return arr;
	                }
	            }
	        });
	```
* Vue 不能检测以下变动的数组
	* 当你利用索引直接设置一个项时，例如： `vm.items[indexOfItem] = newValue`
		* 解决方案如下
			*  Vue.set(example1.items, indexOfItem, newValue) // Vue.set方法
			*  example1.items.splice(indexOfItem, 1, newValue) // Array.prototype.splice`
	* 当你修改数组的长度时，例如： vm.items.length = newLength
		* 解决方法
			* example1.items.splice(newLength)
* 