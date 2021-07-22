# SuperCollider PaneView

examples:

```supercollider
(
w = Window().front;
PaneView(w.view, \horizontal, 5).panes.do({ |p|
    PaneView(p, \vertical, 2.rrand(9)) });
)
```

```supercollider
( // responsive FlowLayout inside panes
w = Window().front;
PaneView(w.view, \horizontal, 2, [1,2]).panes.do({|p|
    var i = ScrollView(p, p.bounds.extent).hasBorder_(false).resize_(5);
    i.addFlowLayout;
    { View(i,100@100).background_(Color.rand) }!10; // populate
    i.onResize_({ |v|
        var d = v.decorator;
        d.bounds = v.bounds.extent@(0@0);
        d.reset;
        v.children.do(d.place(_)) }) });
)
```

```supercollider
( // color fun
w = Window().front;
v = PaneView(w.view, \horizontal, 5, (1..5).scramble, 8,1, Color.rand);
v.panes.do({ |p| var sv = PaneView(p, \vertical, 2.rrand(5), \equal, 8, 1, Color.rand);
    p.background_(Color.rand);
    sv.panes.do({|sp| sp.background_(Color.rand) });
    sv.handles.do({|h| h.background_(Color.rand)}) });
v.handles.do({|h| h.background_(Color.rand)});
)
```

```supercollider
( // example mix with (H|V)Layouts and FlowLayout
w = Window("pane view", { |wh|
    Rect.aboutPoint(Window.availableBounds.center, wh.x/2, wh.y/2)
}.(800@600)).front;
p = PaneView(w.view, \horizontal, 3, [1,2,1]);
Slider2D(p.panes[0], p.panes[0].bounds.extent).resize_(5);
{|v|
    p.panes[1].layout_(VLayout(Knob(), HLayout(Knob(), v)));
    {|p|
        {|p| Knob(p, p.bounds.extent).resize_(5) }.(p.panes[0]);
        {|p| Slider2D(p, p.bounds.extent).resize_(5) }.(p.panes[1]);
    }.(PaneView(v, \vertical, 2));
}.(View());
{|p|
    var sp = PaneView(p, \vertical, 2);
    { |p|
        var i = ScrollView(p, p.bounds.extent).hasBorder_(false).resize_(5);
        i.addFlowLayout;
        i.onResize_({|v| var d = v.decorator;
            d.bounds = v.bounds.extent@(0@0); d.reset; v.children.do(d.place(_))});
        {Knob(i)}!20 }.(sp.panes[0]);
    {|p| p.layout_(HLayout(*({Slider()}!4))) }.(sp.panes[1])
}.(p.panes[2]);
)
```

```supercollider
( // v2 example mix with (H|V)Layouts and FlowLayout
w = Window("pane view", 800@600 !? { |wh|
    Rect.aboutPoint(Window.availableBounds.center, wh.x/2, wh.y/2)
}).front;
p = PaneView(w.view, \horizontal, 3, [1,2,1]);
p.panes[0].layout_(HLayout(Slider2D()));
p.panes[1] !? {|p|
    p.layout_(VLayout(
        Knob(),
        HLayout(
            Knob(),
            PaneView(p, \vertical, 2) !? {|p|
                p.panes[0].layout_(HLayout(Knob()));
                p.panes[1].layout_(HLayout(Slider2D()));
                p.container;
            }))) };
p.panes[2] !? {|p|
    var sp = PaneView(p, \vertical, 2);
    sp.panes[0] !? {|p|
        var i = ScrollView(p, p.bounds.extent).hasBorder_(false).resize_(5);
        i.addFlowLayout;
        i.onResize_({|v| var d = v.decorator;
            d.bounds = v.bounds.extent@(0@0); d.reset; v.children.do(d.place(_))});
        {Knob(i)}!20 };
    sp.panes[1] !? {|p| p.layout_(HLayout(*({Slider()}!4))) } };
)
```

```supercollider
( // using vfrom/hfrom shortcuts
w = Window("pane view", 800@600 !? { |wh|
    Rect.aboutPoint(Window.availableBounds.center, wh.x/2, wh.y/2)
}).front;
w.layout_(HLayout(
    [PaneView.hfrom(*({Slider().orientation_(\vertical)}!10)).asView, stretch:4],
    [PaneView.vfrom(*({Knob()}!10)).asView, stretch:1]
));
)
```
