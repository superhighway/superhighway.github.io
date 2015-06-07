---
title: Rolling Your Own Loading Indicator Using Core Animation
date: 2014-08-03
tags: iOS
---

If you're bored seeing that default `UIActivityIndicator` over and over again, it is time to create your own activity indicator. You might think rolling your own activity indicator is a huge hassle. In reality, a loading indicator is made of combination of shapes being animated. This can be done in your iOS code without animated gif or fancy video!

Generally, you will need to have some mockups in mind, decompose the shapes and animation, then create the implementation. As an example, we'll dabble with `Core Animation` (pure iOS codes!) to make our loading indicator.

## Setting Up the Loading Indicator Class

**How do we decide necessary the abilities of our loading indicator?** Let's mimic `UIActivityIndicator` class definition to provide the properties and methods needed for our loading indicator.

```objc
@interface MyLoadingIndicatorView : UIView
@property (nonatomic, readonly, getter=isAnimating) BOOL animating;

- (void)startAnimating;
- (void)stopAnimating;
@end
```

The `startAnimating` method initiates infinite animation of the indicator. The `stopAnimating` method terminates the infinite animation, preferably using some animation too. The `animating` property explains the indicator state whether it is indeed animating.

## <s>Creating</s> Copying a Loading Indicator (from QuizUp)

<div style="text-align: center; margin-top: 1em;">
  <a href="http://cl.ly/2u1O241x2t2c/QULoadingIndicatorView.mov" target="_blank" alt="A Replica of QuizUp Loading Indicator">
    <img src="/images/2014-08-03-rolling-your-own-loading-indicator-using-core-animation/QULoadingIndicatorView.gif" alt="A Replica of QuizUp Loading Indicator" class="no-border" />
  </a>
  <p style="font-style: italic; font-size: 0.9em;">A Replica of QuizUp Loading Indicator</p>
</div>

When I saw [QuizUp](https://www.quizup.com/en) for the first time, I found its loading indicator really interesting! The indicator consists of two main **shapes**: *circles* and *triangles*. By observing, we can find that...

1. The *circles* scale in turns, changing colors based on an array of colors, cycling back when reaching the end of array.
1. The thunder consists of two *triangles*.

Is everything doable in code? Yes! We can draw the shapes above using `CALayer`, and perform the animation using `CAAnimation`. Here are overall steps.

### 1. Creating a Container View for Both Shapes

![The Container View](2014-08-03-rolling-your-own-loading-indicator-using-core-animation/1-the-container-view.png "The Container View")

Constraints for the container view:

- Top: 120
- Width: 100
- Height: 100
- Horizontal Center in Container: 0 (Centered Horizontally)

![The Container View Constraints](2014-08-03-rolling-your-own-loading-indicator-using-core-animation/1-the-container-view-constraints.png "The Container View Constraints")

Let's call this container view `QULoadingIndicatorView`. We need it because of the following reasons.

- For sake of sanity, the animating circles should be grouped together and separated from the thunder.
- The thunder shape can have different `CGRect` size from the circles.


```objc
// QULoadingIndicatorView.h
@interface QULoadingIndicatorView : UIView
@property (strong, nonatomic) NSArray *colors;
@property (nonatomic, readonly, getter = isAnimating) BOOL animating;

- (void)startAnimating;
- (void)stopAnimating;
@end
```

In the loading indicator implementation file, we will put empty methods for now. These empty methods will forward to our shapes later, once they are ready.

```objc
#import "QUThunderView.h"

@interface QULoadingIndicatorView ()

@end

@implementation QULoadingIndicatorView

- (void)awakeFromNib {
    [super awakeFromNib];
}

- (void)setColors:(NSArray *)colors {
    // Sets colors of animating circles
}

- (NSArray *)colors {
    // Returns colors of animating circles
    return @[];
}

- (BOOL)isAnimating {
    // Returns animation status of the animating circles
    return NO;
}

- (void)startAnimating {
    // Start animating circles and set thunder to be visible
}

- (void)stopAnimating {
    // Stop animating circles and set thunder to be invisible
}

@end
```

### 2. Animating Circles

Let's prepare a view for the animating circles, `QUAlternatingCirclesView`.

![The Alternating Circles View](2014-08-03-rolling-your-own-loading-indicator-using-core-animation/2-the-circles.png "The Alternating Circles View")

Constraints for the alternating circles view (to fill the container):

- Top: 0
- Left: 0
- Right: 0
- Bottom: 0

![The Alternating Circles View Constraints](2014-08-03-rolling-your-own-loading-indicator-using-core-animation/2-the-circles-constraints.png "The Alternating Circles View Constraints")

```objc
@interface QUAlternatingCirclesView : UIView
@property (strong, nonatomic) NSArray *colors;
@property (nonatomic, readonly, getter = isAnimating) BOOL animating;

- (void)startAnimating;
- (void)stopAnimating;
@end
```

<div style="text-align: center; margin-top: 1em;">
  <a href="http://cl.ly/2u1O241x2t2c/QULoadingIndicatorView.mov" target="_blank" alt="A Replica of QuizUp Loading Indicator">
    <img src="/images/2014-08-03-rolling-your-own-loading-indicator-using-core-animation/QULoadingIndicatorView.gif" alt="A Replica of QuizUp Loading Indicator" class="no-border" />
  </a>
  <p style="font-style: italic; font-size: 0.9em;">A Replica of QuizUp Loading Indicator</p>
</div>

Let's observe the animation again. When `startAnimating` is invoked, the circles are scaled alternately from 0 to its container size, 100x100. This means we do scale transform from 0 to identity on each of the circles.

```objc
// Circle shape
CALayer *layer = self.colorLayers[colorLayerIndex];
layer.backgroundColor = [colors[colorIndex] CGColor];
layer.frame = self.layer.bounds;
layer.masksToBounds = YES;
layer.cornerRadius = layer.frame.size.width/2;
layer.transform = CATransform3DMakeScale(0, 0, 0);
[self.layer addSublayer:layer];

// Scale animation
NSTimeInterval duration = 0.7;
CABasicAnimation *anim = [CABasicAnimation animationWithKeyPath:@"transform"];
anim.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
anim.fromValue = [NSValue valueWithCATransform3D:CATransform3DMakeScale(0, 0, 0)];
anim.toValue = [NSValue valueWithCATransform3D:CATransform3DIdentity];
anim.duration = duration;
anim.removedOnCompletion = NO;
anim.fillMode = kCAFillModeForwards;
anim.delegate = self;
[self.animatingColorLayer addAnimation:anim forKey:anim.keyPath];
```

The nagging question that popped to my head was how many `CALayer` is needed to make smooth alternating circles. We don't need layers as many as the colors. Imagine if we want to have 100 colors; having 100 layers is a little bit overkill.

Now let's change the question. How many is the least number of layers we need? We know at least there should be at least 2 (it can be 3 or more though), because we see that one acts as the background, and another one is animating.

#### Experiment 1: Two Instances of CALayer (Back and Animating Layers)

After the first layer is scaled from 0 to its original size, the second layer should immediately scale from 0 to its original size. After the second layer is scaled, the first one is swapped immediately to be on top of the second one. Unfortunately, during this swapping process, the loading indicator looks **blinking**. This gives the impression that the animation is not smooth...


#### Experiment 2: Three Instances of CALayer (Back, Animating, and Front Layers)

This approach is a superset of the first experiment. In addition to the two layers, we have a third layer behind the second layer.

We will still have the same mechanism as experiment 1. The difference is, after the first layer is send to back of all layers:

- the second layer acts as the first layer
- the third layer acts as the second layer
- the first layer acts as the third layer

In other words, regardless of the layers' sequence, we always have animating, front, and back color layers.

```objc
@interface QUAlternatingCirclesView ()
@property (nonatomic, getter = isAnimating) BOOL animating;

@property (strong, nonatomic) NSArray *colorLayers;
@property (nonatomic) NSInteger animatingColorLayerIndex;

@property (strong, nonatomic, readonly) CALayer *backColorLayer;
@property (strong, nonatomic, readonly) CALayer *animatingColorLayer;
@property (strong, nonatomic, readonly) CALayer *frontColorLayer;

@property (nonatomic) NSInteger nextColorIndex;


@end

@implementation QUAlternatingCirclesView

- (CALayer *)backColorLayer
{
    NSInteger backLayerIndex = self.animatingColorLayerIndex - 1;
    if (backLayerIndex < 0) backLayerIndex += self.colorLayers.count;
    return self.colorLayers[backLayerIndex];
}

- (CALayer *)animatingColorLayer
{
    return self.colorLayers[self.animatingColorLayerIndex];
}

- (CALayer *)frontColorLayer
{
    NSInteger frontLayerIndex = (self.animatingColorLayerIndex + 1) % self.colorLayers.count;
    return self.colorLayers[frontLayerIndex];
}

// ...

@end
```

We also have to make sure that the colors assigned to those layers are in sequence of colors given by `QULoadingIndicatorView`.


```objc
// QUAlternatingCirclesView.m

@implementation QUAlternatingCirclesView

// ...

- (void)startAnimating
{
    [self prepareLayers];

    self.animating = YES;
    [self animateFirst];
}

// ...

- (void)prepareLayers
{
    self.colorLayers = @[[CALayer layer], [CALayer layer], [CALayer layer]];
    self.clipsToBounds = YES;

    NSArray *colors = self.colors;
    NSInteger colorsCount = colors.count;
    NSInteger colorIndex = 0;
    for (NSInteger colorLayerIndex = self.colorLayers.count - 1; colorLayerIndex >= 0; --colorLayerIndex) {
        CALayer *layer = self.colorLayers[colorLayerIndex];
        layer.backgroundColor = [colors[colorIndex % colorsCount] CGColor];
        layer.frame = self.layer.bounds;
        layer.masksToBounds = YES;
        layer.cornerRadius = layer.frame.size.width/2;
        layer.transform = CATransform3DMakeScale(0, 0, 0);
        [self.layer addSublayer:layer];
        ++colorIndex;
    }
}

- (void)animateFirst
{
    self.animatingColorLayerIndex = 2;
    self.nextColorIndex = 3;

    [self animateColor];
}

- (void)animateNext
{
    CALayer *backColorLayer = self.backColorLayer;
    backColorLayer.transform = CATransform3DMakeScale(0, 0, 0);
    [self.layer addSublayer:backColorLayer];

    CALayer *frontColorLayer = self.frontColorLayer;
    frontColorLayer.transform = CATransform3DMakeScale(0, 0, 0);
    frontColorLayer.backgroundColor = [self.colors[self.nextColorIndex % self.colors.count] CGColor];

    self.animatingColorLayerIndex = (self.animatingColorLayerIndex - 1);
    if (self.animatingColorLayerIndex < 0) self.animatingColorLayerIndex += self.colorLayers.count;
    self.nextColorIndex = (self.nextColorIndex + 1) % self.colors.count;

    [self animateColor];
}

- (void)animateColor
{
    NSTimeInterval duration = 0.7;
    CABasicAnimation *anim = [CABasicAnimation animationWithKeyPath:@"transform"];
    anim.timingFunction = [CAMediaTimingFunction functionWithName:kCAMediaTimingFunctionEaseOut];
    anim.fromValue = [NSValue valueWithCATransform3D:CATransform3DMakeScale(0, 0, 0)];
    anim.toValue = [NSValue valueWithCATransform3D:CATransform3DIdentity];
    anim.duration = duration;
    anim.removedOnCompletion = NO;
    anim.fillMode = kCAFillModeForwards;
    anim.delegate = self;
    [self.animatingColorLayer addAnimation:anim forKey:anim.keyPath];
}

#pragma mark - CAAnimationDelegate

- (void)animationDidStop:(CAAnimation *)anim finished:(BOOL)finished
{
    if (finished) {
        if (self.isAnimating) {
            [self animateNext];
        } else {
            for (CALayer *colorLayer in self.colorLayers) {
                [colorLayer removeFromSuperlayer];
            }
        }
    }
}

@end

```

The result of experiment 2 is smoother alternating circles than that of experiment 1.

<div style="text-align: center; margin-top: 1em;">
  <a href="http://cl.ly/2u1O241x2t2c/QULoadingIndicatorView.mov" target="_blank" alt="A Replica of QuizUp Loading Indicator">
    <img src="/images/2014-08-03-rolling-your-own-loading-indicator-using-core-animation/QULoadingIndicatorView.gif" alt="A Replica of QuizUp Loading Indicator" class="no-border" />
  </a>
  <p style="font-style: italic; font-size: 0.9em;">A Replica of QuizUp Loading Indicator</p>
</div>

### 3. Making the Thunder Using Triangles

Prepare `QUThunderLayer` for displaying the thunder.

![The Thunder View](2014-08-03-rolling-your-own-loading-indicator-using-core-animation/3-the-thunder.png "The Thunder View")

Constraints for the alternating thunder view (to make it look okay on top of the circles):

- Top: 20
- Left: 30
- Right: 30
- Bottom: 20

![The Thunder View Constraints](2014-08-03-rolling-your-own-loading-indicator-using-core-animation/3-the-thunder-constraints.png "The Thunder View Constraints")

The thunder consist of one triangle on the left and another one on the right. We can use two `UIBezierPath` to create the two triangles.

```objc
// QUThunderView.m

// ...

@implementation QUThunderLayer

// ...

- (void)setupLeftTriangle {
    self.leftTriangleLayer = [CAShapeLayer layer];
    self.leftTriangleLayer.fillColor = [UIColor whiteColor].CGColor;
    UIBezierPath *triangle = [UIBezierPath bezierPath];

    CGFloat minX = 0, minY = 0;
    CGFloat maxX = CGRectGetMidX(self.bounds);
    CGFloat maxY = CGRectGetMaxY(self.bounds)*0.66;
    [triangle moveToPoint:(CGPoint){maxX, minY}];
    [triangle addLineToPoint:(CGPoint){maxX, maxY}];
    [triangle addLineToPoint:(CGPoint){minX, maxY}];
    [triangle closePath];
    self.leftTriangleLayer.path = triangle.CGPath;

    [self addSublayer:self.leftTriangleLayer];
}

- (void)setupRightTriangle {
    self.rightTriangleLayer = [CAShapeLayer layer];
    self.rightTriangleLayer.fillColor = [UIColor whiteColor].CGColor;

    UIBezierPath *triangle = [UIBezierPath bezierPath];

    CGFloat minX = CGRectGetMidX(self.bounds), minY = CGRectGetMaxY(self.bounds)*0.33;
    CGFloat maxX = CGRectGetMaxX(self.bounds);
    CGFloat maxY = CGRectGetMaxY(self.bounds);
    [triangle moveToPoint:(CGPoint){minX, minY}];
    [triangle addLineToPoint:(CGPoint){maxX, minY}];
    [triangle addLineToPoint:(CGPoint){minX, maxY}];
    [triangle closePath];
    self.rightTriangleLayer.path = triangle.CGPath;

    [self addSublayer:self.rightTriangleLayer];
}

@end


@implementation QUThunderView

- (void)awakeFromNib {
    [super awakeFromNib];
    // Add an instance of QUThunderLayer to this view's layer
}

@end
```

## Full Source Code

The rest of the details (like implementation details of `stopAnimating` and `QULoadingIndicatorView`) are available as full source code in [this GitHub repository](http://git.io/CyQvSw). Feel free to run the project to play around with the loading indicator. If you want to use it, copy `QULoadingIndicatorView.*`, `QUThunderView.*`, and `QUAlternatingCirclesView.*` to your project.
