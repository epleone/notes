---
tags:
  - cpp
  - snippets
---

# cmd参数解析Parser

## opencv 的 CommandLineParser

``` c++
cv::CommandLineParser parser(
		argc, argv,
		"{mode m|0|0 for camera,1 for images, 2 for videos }"
		"{path id|<none>|input txt path or video path }"
		"{roll r|0| Roll of camera. Support for 0, 90, 180, 270 }"
		"{model_path mp|<none> | }"
		"{h human| | }"
		"{bs save| | }"
		"{save_path sp|<none> | }"
		"{bd det| | }"
		"{bl log| | }"
		"{vis display| | }"   // -vis --display, 两种调用方式
	); // 注意不要用tab，会影响解析

	parser.about("Hand Gesture Recognition 2D Demo v2.0");
	const RUNNING_MODE mode = RUNNING_MODE(parser.get<int>("mode"));
	string input_path = parser.get<string>("path");
	const int _roll = parser.get<int>("roll");

	g_engine.model_path = parser.get<string>("model_path");
	g_engine.save_path = parser.get<string>("save_path");
	g_engine.b_usehuman = parser.has("human");		  // --human
	g_engine.b_save = parser.has("save");			  // --save
	g_engine.b_useDet = parser.has("det");			  // --det
	g_engine.b_log = parser.has("log");				  // --log
	g_engine.vp.display = parser.has("display");	  // --display
	
```

