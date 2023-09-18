---
layout: post
title: "Parallel Lines in Vega"
date: 2023-07-31 00:00:00 -0700
---

I was working on multi-graph visualizations and I wanted to place multiple edges between two nodes.  This led me down a rabbit hole that included the `atan2` function and a few hours fighting special cases. 

Stepping back, I looked at a vector implementation instead and the solution ended up being surprising simple!  The magic google words were "parallel translation of lines" that got me the to some useful math & Stack Overflow answers ([1](https://math.stackexchange.com/questions/84065/calculate-points-for-a-parallel-line) puts it all in one place, but [2](https://stackoverflow.com/questions/11253626/function-to-translate-end-coordinates-of-a-line)  & [3](https://stackoverflow.com/questions/1243614/how-do-i-calculate-the-normal-vector-of-a-line-segment) helped me see the pattern of _why_ that was the answer). Here is a simple visualization that starts with lines and then calculates parallel lines at a given offset (see it in the [online Vega editor](https://vega.github.io/editor/#/url/vega/N4IgJAzgxgFgpgWwIYgFwhgF0wBwqgegIDc4BzJAOjIEtMYBXAI0poHsDp5kTykSArJQBWENgDsQAGhAB3GgBN6aAMwAGNTPg0yWVRpk4kChTXFk0mkBB3ikAGwhoA2qDsI4aEGwBmPiHCY0iDEDgyeqAAcMkxmCmigZjgMQegATkjmnjIIZmgAtABMVsgAHmjFMhCYcDhoAIwAvo0AujIKSJgoqK4g7hEg9mbZIWFwTj2J8ahW5aj1AlYAng2LMqWF+subqOpWUF5kaXBwko1SU5brq8s36zt7Mks7+17JaTj2nueXM9fzaxAKwBsx2C22VxAB3ScHiPxAikhc3BTzuIA2WyeYMB0IR4lMZDYIHhiL+6LRwJR6JeWLRuKWcHs9jYsmJFwR01mFLRGJBtN2BihXiY9nCbN+XL5QJ5NKBD0FuLYGSy4o5SO5Ut5ELJuJwZgA1sSWvD+l4IDAaD4agp8kNxCMxAw0lABnaRphlRAfEqEC5QJgljgBt60ggGPYUDIkBMQApyjI4KUcGkvB1MAwEJRSvk0xms5t4QGg14Q2GI8Fo6mVgmkyn0LnM0sc50889VUXgz7w5GQJX0F9zMoa8mzQBHNKYAAUOBZk4blDjUgABIUAJRLgDUS5nsjnLczCiWy7Xq-bgc7oe7FZjAEF4yBEyP0M30welgR5wOyMpC+eS13yyjW9qwfWtU33BdSg-CCvx-dkO3-S9AN7GNxHvR861jCDSk3JdfH8QIlwAKiXec7zPYt0FLK8gK8NDNmHTD5w2XD8ICTBiNIiDyN-SiQGo5C+z6ECMPA19KCWVi-HYziyJWXiLzLHshPENtGLE1tCikgiOJIuSjVaHIkDSfUJl6BD0nDEZTX7YYnBkHw0jYX1UFANNukGYY2QfcQoDYBQIlAU4ajrUBqic-VAv4mhGWmIVzmsD02EigB1RRlFc0ZRQiFRmhkBgcDTKK5lAHwYvsOLygS4FSvKuL5PuBJotirwMWqnZapa9A22aPL-T-SyvmCGzrAtK1YVtOzgkc5ymvcs0xutSb7W804-ICprgrgULEoiqKyq6+KZDYIwoDoGqsrFGZKEKAQEvC5K4DSpQYCa0JstUPKQAKoqmpK5qKroqrUUyg7AfQVTvN5Trwb6Nr+RhuLVILXrjSAA)).  

```json
{
  "$schema": "https://vega.github.io/schema/vega/v5.json",
  "width": 300,
  "height": 300,
  "padding": 0,

  "signals": [
    {"name": "offset", "value": 8, "bind": {"input": "range", "min": -20, "max": 20, "step": 1}}
  ],

  "data": [
      {
        "name": "line",
        "values": [
          {"id": 0, "x": 150, "y": 150, "x2": 300, "y2": 300, "c": "green"},
          {"id": 0, "x": 150, "y": 150, "x2": 300, "y2": 0, "c": "purple"},
          {"id": 0, "x": 150, "y": 150, "x2": 150, "y2": 0, "c": "red"},
          {"id": 0, "x": 150, "y": 150, "x2": 300, "y2": 150, "c": "indigo"},
          {"id": 0, "x": 150, "y": 150, "x2": 0, "y2": 150, "c": "yellow"},
          {"id": 0, "x": 150, "y": 150, "x2": 150, "y2": 300, "c": "blue"},
          {"id": 0, "x": 150, "y": 150, "x2": 0, "y2": 300, "c": "orange"},
          {"id": 0, "x": 150, "y": 150, "x2": 0, "y2": 0, "c": "pink"}
        ]
      },
      {
        "name": "shifted-line",
        "source": "line",
        "transform": [
          {"type": "formula", "as": "dx", "expr": "datum.x-datum.x2"},
          {"type": "formula", "as": "dy", "expr": "datum.y-datum.y2"},
          {"type": "formula", "as": "length", "expr": "sqrt(pow(datum.dx, 2) + pow(datum.dy, 2))"},
          {"type": "formula", "as": "Ax", "expr": "-datum.dy/datum.length"},
          {"type": "formula", "as": "Ay", "expr": "datum.dx/datum.length"},
          {"type": "formula", "as": "nx", "expr": "datum.x + offset * datum.Ax"},
          {"type": "formula", "as": "nx2", "expr": "datum.x2 + offset * datum.Ax"},
          {"type": "formula", "as": "ny", "expr": "datum.y + offset * datum.Ay"},
          {"type": "formula", "as": "ny2", "expr": "datum.y2 + offset * datum.Ay"}
        ]
      }
    ],

  "marks": [
    {
      "type": "rule",
      "name": "lines",
      "from": {"data": "line"},
      "encode": {
        "enter": {
          "stroke": {"field": "c"},
          "strokeWidth": {"value": 3}
        },
        "update": {
          "x": {"field": "x"},
          "y": {"field": "y"},
          "x2": {"field": "x2"},
          "y2": {"field": "y2"}
        }
      }
    },
    {
      "type": "rule",
      "name": "shifted-lines",
      "from": {"data": "shifted-line"},
      "encode": {
        "enter": {
          "stroke": {"field": "c"},
          "opacity": {"value": 0.25},
          "strokeWidth": {"value": 3}
        },
        "update": {
          "x": {"field": "nx"},
          "y": {"field": "ny"},
          "x2": {"field": "nx2"},
          "y2": {"field": "ny2"}
        }
      }
    }    
  ]    
}
```

The work is being done in the `shifted-lines` data entry:
- `dx` and `dy` are the change in x and y in the original lines.
- `length` is used to normalize shifts based on `dx` and `dy`.  The normalization ensures the line shift is the same, regardless of the length of the original line.
- `Ax` and `Ay` are the most interesting parts.  `Ax` is roughly the amount of shift in the `x` direction, given how much change in `y` there to get a single unit of change.  This achieves "Parallel translation", so the shift distance **total** is the same, regardless of the angle.
- Finally `nx`, `ny`, `nx2`, `ny2` are the "new" coordinates if you shift the original values by the desired distance.

In a more mathematical form:
- $length = \sqrt{(y_2-y_1)^2 + (x_2-x_1)^2}$
- $nx = x + offset * \frac{-(y_1-y_2)}{length}$
- $ny = y + offset * \frac{(x_1-x_2)}{length}$
- $nx_2$ and $ny_2$  are computed similarly from $x_2$ and $y_2$


## Addendum 

For the those that find "bad code written in a JSON format" amusing (?), here is the most critical part of my best attempt at the parallel lines using `atan2`. (Substitute this block for the `transform` block above and...enjoy a good laugh?)

```json
	"transform": [
	  {"type": "formula", "as": "alpha",  "expr": "atan2(datum.y, datum.x)"},
	  {"type": "formula", "as": "d_intercept", "expr": "offset * sin(datum.alpha)"},
	  {"type": "formula", "as": "slope", "expr": "(datum.y2 - datum.y)/(datum.x2 - datum.x)"},
	  {"type": "formula", "as": "new_intercept", 
	   "expr": "datum.d_intercept + (datum.y-(datum.slope*datum.x))"},
	  {"type": "formula", "as": "_dy", 
	   "expr": "datum.y-(datum.slope*datum.x+datum.new_intercept)"},
	  {"type": "formula", "as": "_dx", 
	   "expr": "if(datum.slope !=0, datum.x - (datum.y-datum.new_intercept)/datum.slope, 0)"},
	  {"type": "formula", "as": "dy", 
	   "expr": "if(isFinite(datum._dy), datum._dy, 0)"},
	  {"type": "formula", "as": "dx", 
	   "expr": "if(isFinite(datum._dx), datum._dx, offset)"},
	  {"type": "formula", "as": "nx", "expr": "datum.x+datum.dx"},
	  {"type": "formula", "as": "nx2", "expr": "datum.x2+datum.dx"},
	  {"type": "formula", "as": "ny", "expr": "datum.y+datum.dy"},
	  {"type": "formula", "as": "ny2", "expr": "datum.y2+datum.dy"}
	]
```
