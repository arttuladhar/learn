---
title: Arrow Functions
---

Arrow Function Express allows you to write shorter syntax than it's predecessor Function expression. In addition and more exciting is how the new Arrow function bind their context.

{{< highlight javascript>}}
(param1, param2, param3) => {
    statements
}

singleParam => { statements }

() => { statements }
{{< / highlight >}}

### Example

{{< highlight javascript>}}
var materials = [
    'Iron', 'Calcium', 'Sodium', 'Magnanese'
]

materials.map(material => material.length)
{{< / highlight >}}

An arrow function does not newly define its own this when it's being executed.The value of this is always inherited from the enclosing scope.

{{< highlight javascript>}}
// ES5
function CounterES5(){
   this.seconds = 0;
   window.setInterval(function() {
    this.seconds++;
    console.log(seconds);
  }.bind(this), 1000);
}
//ES6
function CounterES6(){
   this.seconds =0;
   window.setInterval( () => {
     this.seconds++;
     console.log(seconds)
   },1000 );
}
{{< / highlight >}}