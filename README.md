# Sankey Diagrams

## Demo

- <http://bost.ocks.org/mike/sankey/>
- see the `examples` folder

![Captain Sankey Diagram Example](https://cloud.githubusercontent.com/assets/44946/8294625/08e5068c-193e-11e5-91f1-16dd6b72c0ec.png)

- Leave Path Attribute Newly Added By Hunjae Jung (`examples/example03-basic-with-leave-path.html`)

![With Leave Path](http://hunjae.com/content/images/2015/09/-----2015-09-11-15-07-45.png)

## Basic usage

Expected data (format): arrays of nodes and links.

```js
// Array of node objects (e.g. with a 'name' field here):
var nodes = [
    {name: "a"},
    {name: "b"},
    // ...
];
// Array of links between the nodes:
// objects with 'source', 'target' node indices and a 'value' for the link weight:
var links = [
    {source: 0, target: 1, value: 10.2},
    {source: 0, target: 2, value: 5.4}
    // ...
];
```

Configure the Sankey object and get a path data generator.

```js
var sankey = d3.sankey()
    .size([width, height])
    .nodeWidth(15)
    .nodePadding(10)
    .nodes(nodes)
    .leavePath(true) // if you want to show leave path
    .links(links)
    .layout(32);

var path = sankey.link();
```

Draw links and nodes, the bare essence:

```js
// Set up Leave Path Color
var linearGradient = svg.append('defs')
  .append('linearGradient')
  .attr('id','leavePathGrad')
  .attr('x1','0%')
  .attr('y1','0%')
  .attr('x2','0%')
  .attr('y2','100%');

  linearGradient.append('stop')
  .attr('offset','0%')
  .style('stop-color','rgb(255,0,0)')
  .style('stop-opacity','1')

  linearGradient.append('stop')
  .attr('offset','100%')
  .style('stop-color','rgb(255,255,255)')
  .style('stop-opacity','1')

// Draw links (as svg paths)
root.selectAll('.link')
    .data(links)
    .enter().append('path')
    .attr('class', 'link')
    .attr('d', path)
    .style("stroke-width", function (d) {
      if(!d.isLeavePath){
        return Math.max(1, d.dy);
      }else{
        return 1;
      }
    })
    .style('stroke', function(d){
        if(d.isLeavePath){
            return 'url(#leavePathGrad)';
        }
    })
    .style('fill', function(d){
        if(d.isLeavePath){
            return 'url(#leavePathGrad)';
        }
    });
    // ...

// Draw nodes
root.selectAll('.node')
    .data(nodes)
    .enter().append('rect')
    .attr({
        'class': 'node',
        x: function (d) { return d.x },
        y: function (d) { return d.y },
        height: function (d) { return d.dy; },
        width: sankey.nodeWidth()
    })
    // ...
```
