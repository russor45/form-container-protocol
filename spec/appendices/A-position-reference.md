## Appendix A: Position Reference

### Center Position

Container positioned centrally within the viewport or parent boundary. Commonly implemented as modal dialogs with overlay backgrounds.

```
┌─────────────────────────────────┐
│           Viewport              │
│                                 │
│        ┌─────────────┐          │
│        │   Center    │          │
│        │   Container │          │
│        │             │          │
│        └─────────────┘          │
│                                 │
└─────────────────────────────────┘
```

### Left Position

Container anchored to the left edge of the viewport or parent boundary. Commonly implemented as left sidebars or slide-out panels.

```
┌─────────────────────────────────┐
│┌──────────┐                     │
││   Left   │    Main Content     │
││Container │                     │
││          │                     │
││          │                     │
│└──────────┘                     │
└─────────────────────────────────┘
```

### Right Position

Container anchored to the right edge of the viewport or parent boundary. Commonly implemented as right sidebars or slide-out panels.

```
┌─────────────────────────────────┐
│                    ┌──────────┐ │
│   Main Content     │  Right   │ │
│                    │Container │ │
│                    │          │ │
│                    │          │ │
│                    └──────────┘ │
└─────────────────────────────────┘
```

### Top Position

Container anchored to the top edge of the viewport or parent boundary. Commonly implemented as top drawers or notification panels.

```
┌─────────────────────────────────┐
│┌───────────────────────────────┐│
││        Top Container          ││
│└───────────────────────────────┘│
│                                 │
│         Main Content            │
│                                 │
│                                 │
└─────────────────────────────────┘
```

### Bottom Position

Container anchored to the bottom edge of the viewport or parent boundary. Commonly implemented as bottom drawers or mobile sheets.

```
┌─────────────────────────────────┐
│                                 │
│         Main Content            │
│                                 │
│                                 │
│┌───────────────────────────────┐│
││       Bottom Container        ││
│└───────────────────────────────┘│
└─────────────────────────────────┘
```

### Inline Position

Container embedded within the natural document flow. Commonly implemented as expandable sections or edit-in-place forms.

```
┌─────────────────────────────────┐
│  Content Above                  │
│                                 │
│ ┌─────────────────────────────┐ │
│ │     Inline Container        │ │
│ │                             │ │
│ └─────────────────────────────┘ │
│                                 │
│  Content Below                  │
└─────────────────────────────────┘
```

---