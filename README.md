# 网页小配件之日历

![日历](https://github.com/AARON-CHENMY/webcalendar/blob/master/calendar.png"calendar")

## 动机

- 逛别人的博客的时候总会看到有个日历，觉得有意思，所以动手做一个给自己

## 过程

在网上查找了一番资料之后，知道了原理，于是动手写写自己的。

### 结构——HTML
- 首先日历整体作为一个大模块(div)，其中可分为两大部分——calendartitle和calendarbody，calendartitle包括年月的显示和上一月、下一月的切换按钮；calendarbody包括星期天到星期一的名称显示和日期的按月份显示

```html
<div class="calendar">
	<div class="calendartitle">
	...
	</div>
	<div class="calendarbody">
	...
	</div>
</div>	
```

### 表现——CSS
- 在日历类中，我设置了高度、宽度、字体大小，由于继承关系，在它之下的子孙元素会继承这些属性值，所以在它的子孙元素的样式规则中，涉及到高度、宽度、字体大小时我都使用了百分比和em，这样，只要上层的.calendar变化，其它的元素大小也会跟着变化。(注：显示效果中边框的样式选择了ridge)
```css
.calendar{
	height: 400px;   
	width: 400px;  
	font-size:15px;   
	border:8px solid #CDBA96;  
	border-radius: 20px;
	border-style: ridge;
}
```
- 切换上一月与下一月的两个按钮需要采用定位技术：position；
将position属性设置成为relative，这样，就可以让它脱离正常文档流，相对于它在正常文档流中的位置进行定位，还要配合top和left的使用：
```css
#prev{                                             
	position: relative;
	top:-2.5em;
	left: -5em;
}
#next{
	position: relative;
	top:-2.5em;
	left: 5em;
}
```
- 下面日期的月份显示形式使用的是无序列表ul，然后将它的列表项li浮动起来，一个接一个的排下去，形成日期表：

```css
.bodylist ul li{
	width:14.28%;
	height: 2.6em;
	display:block;
	float:left;
	list-style-type:none;
	line-height: 2.6em;
	box-sizing:border-box;
	text-align:center;
}
```

- 为了方便javascript执行时动态的变化，这里预先设置的颜色类，在javascript中要对不同元素应用不同的颜色时，只需将它的class名设置成以下对应的就行。
```
/*颜色设置类*/
.lightgrey{
	color:#a8a8a8; /*浅灰色*/
}
.darkgrey{
	color:#565656; /*深灰色*/
}
.lightbrown{
	color:#CDBA96; /*绿色*/
}
.greenbox{
	background:#e9f8df; /*浅绿色背景*/
}
```


### 行为——JavaScript
- 要想显示日期，自然需要用到JavaScript的Date类，通过它的构造函数可以新建一个Date的实例，我们需要用到它的一些方法：包括
```javascript
var my_date = new Date();
var my_year = my_date.getFullYear();
var my_month = my_date.getMonth();
var my_day = my_date.getDate();
```
- 还需要用到它的另一个方法来得到某一月的第一天是星期几，getDay方法返回0-6，分别对应于星期天到星期六：
```
function startDay(year, month){        
	var tmp = new Date(year, month, 1);
	return (tmp.getDay());
}
```
我们还要判断是闰年还是非闰年，因为有个二月特殊：
```javascript
/*判断是否为闰年，得到该月有几天
能被4整除而不能被100整除的，或者是能被400整除的
设置两个判定条件的原因：如1800年不是闰年，但能被4整除*/
function daysOfMonth(year, month){
	if( (year%4==0 && year%100!=0 )|| year%400==0 ){
		return (daysInMonthLeap[month]);
	}else{
		return (daysInMonthNormal[month]);
	}
}
```

- 做好这些准备后，就可以开始尝试生成日历了
```javascript
function generateDate(){
	var displayStr="";
	var daysSum = daysOfMonth(my_year, my_month);
	var firstDay = startDay(my_year, my_month);
	var myclass;
	//某月第一天星期几，在它之前空多少天就需要多少个li占位
	for(var i=0; i<firstDay; i++){
		displayStr += "<li></li>"; 
	}
	for(var i=1; i<=daysSum; i++){
		if( (i<my_day && my_year==my_date.getFullYear()&&my_month==my_date.getMonth()) ||my_year<my_date.getFullYear() ||(my_year==my_date.getFullYear()&&my_month<my_date.getMonth())){
			myclass = " class='lightgrey'";   //当天之前
		}else if ( i==my_day && my_year==my_date.getFullYear() && my_month==my_date.getMonth()){
			myclass = " class='lightbrown greenbox'";    //当天
		}else{
			myclass = " class='darkgrey'";       // 当天之后
		}
		displayStr += "<li" + myclass + ">" + i +"</li>"; 
	}
	container.innerHTML = displayStr;
	titleMonth.innerHTML = monthName[my_month];
	titleYear.innerHTML= my_year;
}
```
- 这样，日历的显示就完成了，对了，别忘了还有两个按钮呢，当点击时，改变变量my_year和变量my_month的值便可：
```javascript
prevMonth.onclick = function(ev){
	ev.preventDefault();
	my_month--;
	if(my_month<0){
		my_year--;
		my_month = 11;
	}
	generateDate();
}
nextMonth.onclick = function(ev){
	ev.preventDefault();
	my_month++;
	if(my_month>11){
		my_year++;
		my_month=0;
	}
	generateDate();
}
```
