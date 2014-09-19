

<script src="../gitbook/plugins/gitbook-plugin-ggvis/lib/jquery/jquery.min.js"></script>
<link href="../gitbook/plugins/gitbook-plugin-ggvis/lib/jquery-ui/css/smoothness/jquery-ui-1.10.4.custom.min.css" rel="stylesheet" />
<script src="../gitbook/plugins/gitbook-plugin-ggvis/lib/jquery-ui/js/jquery-ui-1.10.4.custom.min.js"></script>
<script src="../gitbook/plugins/gitbook-plugin-ggvis/lib/d3/d3.min.js"></script>
<script src="../gitbook/plugins/gitbook-plugin-ggvis/lib/vega/vega.min.js"></script>
<script src="../gitbook/plugins/gitbook-plugin-ggvis/lib/lodash/lodash.min.js"></script>
<script>var lodash = _.noConflict();</script>
<link href="../gitbook/plugins/gitbook-plugin-ggvis/ggvis/css/ggvis.css" rel="stylesheet" />
<script src="../gitbook/plugins/gitbook-plugin-ggvis/ggvis/js/ggvis.js"></script>
<script src="../gitbook/plugins/gitbook-plugin-ggvis/ggvis/js/shiny-ggvis.js"></script>

# ggvis

`ggvis` is designed to work well with pipes since every function returns the modified `ggvis` as an output.  Let's look at a very simple example `ggvis` pipeline using the `Orange` dataset built into R. We will pipe `Orange` into `ggvis` and then pipe the `ggvis` to `layer_lines()` for a simple line chart.



```r
Orange %>>% ggvis( ~age, ~circumference, stroke =~ Tree ) %>>% layer_lines()
```

`Pipe()` also works well with `ggvis`.


```r
Pipe(Orange)$ggvis( ~age, ~circumference, stroke =~ Tree )$layer_lines()
```

<div id="plot_id441530377-container" class="ggvis-output-container">
<div id="plot_id441530377" class="ggvis-output"></div>
<div class="plot-gear-icon">
<nav class="ggvis-control">
<a class="ggvis-dropdown-toggle" title="Controls" onclick="return false;"></a>
<ul class="ggvis-dropdown">
<li>
Renderer: 
<a id="plot_id441530377_renderer_svg" class="ggvis-renderer-button" onclick="return false;" data-plot-id="plot_id441530377" data-renderer="svg">SVG</a>
 | 
<a id="plot_id441530377_renderer_canvas" class="ggvis-renderer-button" onclick="return false;" data-plot-id="plot_id441530377" data-renderer="canvas">Canvas</a>
</li>
<li>
<a id="plot_id441530377_download" class="ggvis-download" data-plot-id="plot_id441530377">Download</a>
</li>
</ul>
</nav>
</div>
</div>
<script type="text/javascript">
var plot_id441530377_spec = {
	"data" : [
		{
			"name" : ".0/regroup1/arrange2_flat",
			"format" : {
				"type" : "csv",
				"parse" : {
					"age" : "number",
					"circumference" : "number"
				}
			},
			"values" : "\"Tree\",\"age\",\"circumference\"\n\"3\",118,30\n\"3\",484,51\n\"3\",664,75\n\"3\",1004,108\n\"3\",1231,115\n\"3\",1372,139\n\"3\",1582,140\n\"1\",118,30\n\"1\",484,58\n\"1\",664,87\n\"1\",1004,115\n\"1\",1231,120\n\"1\",1372,142\n\"1\",1582,145\n\"5\",118,30\n\"5\",484,49\n\"5\",664,81\n\"5\",1004,125\n\"5\",1231,142\n\"5\",1372,174\n\"5\",1582,177\n\"2\",118,33\n\"2\",484,69\n\"2\",664,111\n\"2\",1004,156\n\"2\",1231,172\n\"2\",1372,203\n\"2\",1582,203\n\"4\",118,32\n\"4\",484,62\n\"4\",664,112\n\"4\",1004,167\n\"4\",1231,179\n\"4\",1372,209\n\"4\",1582,214"
		},
		{
			"name" : ".0/regroup1/arrange2",
			"source" : ".0/regroup1/arrange2_flat",
			"transform" : [
				{
					"type" : "treefacet",
					"keys" : [
						"data.Tree"
					]
				}
			]
		},
		{
			"name" : "scale/stroke",
			"format" : {
				"type" : "csv",
				"parse" : null
			},
			"values" : "\"domain\"\n\"3\"\n\"1\"\n\"5\"\n\"2\"\n\"4\""
		},
		{
			"name" : "scale/x",
			"format" : {
				"type" : "csv",
				"parse" : {
					"domain" : "number"
				}
			},
			"values" : "\"domain\"\n44.8\n1655.2"
		},
		{
			"name" : "scale/y",
			"format" : {
				"type" : "csv",
				"parse" : {
					"domain" : "number"
				}
			},
			"values" : "\"domain\"\n20.8\n223.2"
		}
	],
	"scales" : [
		{
			"name" : "stroke",
			"type" : "ordinal",
			"domain" : {
				"data" : "scale/stroke",
				"field" : "data.domain"
			},
			"points" : true,
			"sort" : false,
			"range" : "category10"
		},
		{
			"name" : "x",
			"domain" : {
				"data" : "scale/x",
				"field" : "data.domain"
			},
			"zero" : false,
			"nice" : false,
			"clamp" : false,
			"range" : "width"
		},
		{
			"name" : "y",
			"domain" : {
				"data" : "scale/y",
				"field" : "data.domain"
			},
			"zero" : false,
			"nice" : false,
			"clamp" : false,
			"range" : "height"
		}
	],
	"marks" : [
		{
			"type" : "group",
			"from" : {
				"data" : ".0/regroup1/arrange2"
			},
			"marks" : [
				{
					"type" : "line",
					"properties" : {
						"update" : {
							"stroke" : {
								"scale" : "stroke",
								"field" : "data.Tree"
							},
							"x" : {
								"scale" : "x",
								"field" : "data.age"
							},
							"y" : {
								"scale" : "y",
								"field" : "data.circumference"
							}
						},
						"ggvis" : {
							"data" : {
								"value" : ".0/regroup1/arrange2"
							}
						}
					}
				}
			]
		}
	],
	"width" : null,
	"height" : null,
	"legends" : [
		{
			"orient" : "right",
			"stroke" : "stroke",
			"title" : "Tree"
		}
	],
	"axes" : [
		{
			"type" : "x",
			"scale" : "x",
			"orient" : "bottom",
			"layer" : "back",
			"grid" : true,
			"title" : "age"
		},
		{
			"type" : "y",
			"scale" : "y",
			"orient" : "left",
			"layer" : "back",
			"grid" : true,
			"title" : "circumference"
		}
	],
	"padding" : null,
	"ggvis_opts" : {
		"keep_aspect" : false,
		"resizable" : true,
		"padding" : {},
		"duration" : 250,
		"renderer" : "svg",
		"hover_duration" : 0,
		"width" : null,
		"height" : null
	},
	"handlers" : null
};
ggvis.getPlot("plot_id441530377").parseSpec(plot_id441530377_spec);
</script>

## Styling `ggvis`

When we prepare a chart for presentation, we usually will want to customize our chart by making various aesthetic changes.  This a great workflow for a pipe.  Let's add a title, clean up our `x` and `y` axes, and sort our legend all in one pipe.


```r
Orange %>>%
  ggvis(~age, ~circumference, stroke =~ Tree ) %>>%
  layer_lines() %>>%
  add_axis("x"
    , title = "Age of Tree"
    , ticks = 6
    , subdivide = 8
    , tick_size_minor = 4
  ) %>>%
  add_axis( "y", title = "Circumference of Trunk (mm)" ) %>>%
  add_legend( "stroke", values = sort(levels(Orange$Tree)) )
```

<div id="plot_id615806440-container" class="ggvis-output-container">
<div id="plot_id615806440" class="ggvis-output"></div>
<div class="plot-gear-icon">
<nav class="ggvis-control">
<a class="ggvis-dropdown-toggle" title="Controls" onclick="return false;"></a>
<ul class="ggvis-dropdown">
<li>
Renderer: 
<a id="plot_id615806440_renderer_svg" class="ggvis-renderer-button" onclick="return false;" data-plot-id="plot_id615806440" data-renderer="svg">SVG</a>
 | 
<a id="plot_id615806440_renderer_canvas" class="ggvis-renderer-button" onclick="return false;" data-plot-id="plot_id615806440" data-renderer="canvas">Canvas</a>
</li>
<li>
<a id="plot_id615806440_download" class="ggvis-download" data-plot-id="plot_id615806440">Download</a>
</li>
</ul>
</nav>
</div>
</div>
<script type="text/javascript">
var plot_id615806440_spec = {
	"data" : [
		{
			"name" : ".0/regroup1/arrange2_flat",
			"format" : {
				"type" : "csv",
				"parse" : {
					"age" : "number",
					"circumference" : "number"
				}
			},
			"values" : "\"Tree\",\"age\",\"circumference\"\n\"3\",118,30\n\"3\",484,51\n\"3\",664,75\n\"3\",1004,108\n\"3\",1231,115\n\"3\",1372,139\n\"3\",1582,140\n\"1\",118,30\n\"1\",484,58\n\"1\",664,87\n\"1\",1004,115\n\"1\",1231,120\n\"1\",1372,142\n\"1\",1582,145\n\"5\",118,30\n\"5\",484,49\n\"5\",664,81\n\"5\",1004,125\n\"5\",1231,142\n\"5\",1372,174\n\"5\",1582,177\n\"2\",118,33\n\"2\",484,69\n\"2\",664,111\n\"2\",1004,156\n\"2\",1231,172\n\"2\",1372,203\n\"2\",1582,203\n\"4\",118,32\n\"4\",484,62\n\"4\",664,112\n\"4\",1004,167\n\"4\",1231,179\n\"4\",1372,209\n\"4\",1582,214"
		},
		{
			"name" : ".0/regroup1/arrange2",
			"source" : ".0/regroup1/arrange2_flat",
			"transform" : [
				{
					"type" : "treefacet",
					"keys" : [
						"data.Tree"
					]
				}
			]
		},
		{
			"name" : "scale/stroke",
			"format" : {
				"type" : "csv",
				"parse" : null
			},
			"values" : "\"domain\"\n\"3\"\n\"1\"\n\"5\"\n\"2\"\n\"4\""
		},
		{
			"name" : "scale/x",
			"format" : {
				"type" : "csv",
				"parse" : {
					"domain" : "number"
				}
			},
			"values" : "\"domain\"\n44.8\n1655.2"
		},
		{
			"name" : "scale/y",
			"format" : {
				"type" : "csv",
				"parse" : {
					"domain" : "number"
				}
			},
			"values" : "\"domain\"\n20.8\n223.2"
		}
	],
	"scales" : [
		{
			"name" : "stroke",
			"type" : "ordinal",
			"domain" : {
				"data" : "scale/stroke",
				"field" : "data.domain"
			},
			"points" : true,
			"sort" : false,
			"range" : "category10"
		},
		{
			"name" : "x",
			"domain" : {
				"data" : "scale/x",
				"field" : "data.domain"
			},
			"zero" : false,
			"nice" : false,
			"clamp" : false,
			"range" : "width"
		},
		{
			"name" : "y",
			"domain" : {
				"data" : "scale/y",
				"field" : "data.domain"
			},
			"zero" : false,
			"nice" : false,
			"clamp" : false,
			"range" : "height"
		}
	],
	"marks" : [
		{
			"type" : "group",
			"from" : {
				"data" : ".0/regroup1/arrange2"
			},
			"marks" : [
				{
					"type" : "line",
					"properties" : {
						"update" : {
							"stroke" : {
								"scale" : "stroke",
								"field" : "data.Tree"
							},
							"x" : {
								"scale" : "x",
								"field" : "data.age"
							},
							"y" : {
								"scale" : "y",
								"field" : "data.circumference"
							}
						},
						"ggvis" : {
							"data" : {
								"value" : ".0/regroup1/arrange2"
							}
						}
					}
				}
			]
		}
	],
	"width" : null,
	"height" : null,
	"legends" : [
		{
			"orient" : "right",
			"values" : [
				"1",
				"2",
				"3",
				"4",
				"5"
			],
			"stroke" : "stroke",
			"title" : "Tree"
		}
	],
	"axes" : [
		{
			"type" : "x",
			"scale" : "x",
			"orient" : "bottom",
			"title" : "Age of Tree",
			"ticks" : 6,
			"subdivide" : 8,
			"tickSizeMinor" : 4,
			"layer" : "back",
			"grid" : true
		},
		{
			"type" : "y",
			"scale" : "y",
			"orient" : "left",
			"title" : "Circumference of Trunk (mm)",
			"layer" : "back",
			"grid" : true
		}
	],
	"padding" : null,
	"ggvis_opts" : {
		"keep_aspect" : false,
		"resizable" : true,
		"padding" : {},
		"duration" : 250,
		"renderer" : "svg",
		"hover_duration" : 0,
		"width" : null,
		"height" : null
	},
	"handlers" : null
};
ggvis.getPlot("plot_id615806440").parseSpec(plot_id615806440_spec);
</script>


