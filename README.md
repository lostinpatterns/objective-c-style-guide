# dailymile Objective-C Style Guide

The style guide was originally forked from [The New York Times' Objective-C Style Guide](https://github.com/NYTimes/objetive-c-style-guide). Thanks to the [previous contributors](https://github.com/NYTimes/objective-c-style-guide/contributors) for the great starting point.

## Introduction

Here are some of the documents from Apple that informed the style guide. If something isn't mentioned here, it's probably covered in great detail in one of these:

* [The Objective-C Programming Language](http://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/ObjectiveC/Introduction/introObjectiveC.html)
* [Cocoa Fundamentals Guide](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CocoaFundamentals/Introduction/Introduction.html)
* [Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [iOS App Programming Guide](http://developer.apple.com/library/ios/#documentation/iphone/conceptual/iphoneosprogrammingguide/Introduction/Introduction.html)

## Table of Contents

* [Dot-Notation Syntax](#dot-notation-syntax)
* [Spacing](#spacing)
* [Conditionals](#conditionals)
  * [Ternary Operator](#ternary-operator)
* [Error handling](#error-handling)
* [Methods](#methods)
  * [Private Methods](#private-methods)
* [Variables](#variables)
* [Naming](#naming)
* [Comments and Documentation](#comments-and-documentation)
* [Init & Dealloc](#init-and-dealloc)
* [Literals](#literals)
* [CGRect Functions](#cgrect-functions)
* [Constants](#constants)
* [Enumerated Types](#enumerated-types)
* [Properties](#properties)
  * [Private Properties](#private-properties)
* [Booleans](#booleans)
* [Singletons](#singletons)
* [Categories](#categories)
* [Blocks](#blocks)
* [Subclassing UIView](#subclassing-uiview)
* [Xcode Project](#xcode-project)

## Dot-Notation Syntax

Dot-notation should **always** be used for accessing and mutating properties. Bracket notation is preferred in all other instances.

**Example:**
```objc
view.backgroundColor = [UIColor orangeColor];
[UIApplication sharedApplication].delegate;
```

**Not:**
```objc
[view setBackgroundColor:[UIColor orangeColor]];
UIApplication.sharedApplication.delegate;
```

## Spacing

* Indent using 2 spaces. Never indent with tabs. Be sure to set this preference in Xcode.
* Method braces and other braces (`if`/`else`/`switch`/`while`/`for` etc.) always open on the same line as the statement but close on a new line except in the case of `else`.

**Example:**
```objc
if (user.isHappy) {
//Do something
} else {
//Do something else
}
```
* There should be exactly one blank line between methods to aid in visual clarity and organization. Whitespace within methods should separate functionality, but often there should probably be new methods.
* `@synthesize` and `@dynamic` should each be declared on new lines in the implementation.

## Conditionals

Conditional bodies should always use braces even when a conditional body could be written without braces (e.g., it is one line only) to prevent [errors](https://github.com/NYTimes/objective-c-style-guide/issues/26#issuecomment-22074256). These errors include adding a second line and expecting it to be part of the if-statement. Another, [even more dangerous defect](http://programmers.stackexchange.com/a/16530) may happen where the line "inside" the if-statement is commented out, and the next line unwittingly becomes part of the if-statement. In addition, this style is more consistent with all other conditionals, and therefore more easily scannable.

**Example:**
```objc
if(!showAll) {
  descriptions = Underscore.head(descriptions, 2);
}
```

**Not:**
```objc
if(!showAll)
  descriptions = Underscore.head(descriptions, 2);
```

The only exception to this rule is when you're returning from a method, it is acceptable to use a one-liner:

**Example:**
```objc
if (!error) return success;
```

### Ternary Operator

The Ternary operator, ? , should only be used when it increases clarity or code neatness. A single condition is usually all that should be evaluated. Evaluating multiple conditions is usually more understandable as an if statement, or refactored into instance variables.

**Example:**
```objc
result = a > b ? x : y;
```

**Not:**
```objc
result = a > b ? x = c > d ? c : d : y;
```

## Error handling

When methods return an error parameter by reference, switch on the returned value, not the error variable.

**Example:**
```objc
NSError *error;
if (![self trySomethingWithError:&error]) {
  // Handle Error
}
```

**Not:**
```objc
NSError *error;
[self trySomethingWithError:&error];
if (error) {
  // Handle Error
}
```

Some of Apple’s APIs write garbage values to the error parameter (if non-NULL) in successful cases, so switching on the error can cause false negatives (and subsequently crash).

## Methods

In method signatures, there should be a space after the scope (-/+ symbol). There should be a space between the method segments.

**Example**:
```objc
- (void)setExampleText:(NSString *)text image:(UIImage *)image;
```

### Private Methods

Private methods signatures should always begin with an underscore.

**Example**:
```objc
- (void)_setExampleText:(NSString *)text image:(UIImage *)image;
```

## Variables

Variables should be named as descriptively as possible. Single letter variable names should be avoided except in `for()` loops.

Asterisks indicating pointers belong with the variable, e.g., `NSString *text` not `NSString* text` or `NSString * text`, except in the case of constants.

Property definitions should be used in place of naked instance variables whenever possible. Direct instance variable access should be avoided except in initializer methods (`init`, `initWithCoder:`, etc…), `dealloc` methods and within custom setters and getters. For more information on using Accessor Methods in Initializer Methods and dealloc, see [here](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6).

**Example:**

```objc
@interface DMSSection: NSObject

@property (nonatomic) NSString *headline;

@end
```

**Not:**

```objc
@interface DMSSection : NSObject {
  NSString *headline;
}
```

## Naming

Apple naming conventions should be adhered to wherever possible, especially those related to [memory management rules](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html) ([NARC](http://stackoverflow.com/a/2865194/340508)).

Long, descriptive method and variable names are good.

**Example:**

```objc
UIButton *settingsButton;
```

**Not**

```objc
UIButton *setBut;
```

A three letter prefix of `DMS` should always be used for class names and constants, however may be omitted for Core Data entity names and when only used within an implementation. Constants should be camel-case with all words capitalized, prefixed by a lowercase `k` and include the related class name for clarity.

**Example:**

```objc
static const NSTimeInterval DMSArticleViewControllerNavigationFadeAnimationDuration = 0.3;
```

**Not:**

```objc
static const NSTimeInterval fadetime = 1.7;
```

Properties and local variables should be camel-case with the leading word being lowercase. 

Instance variables should be camel-case with the leading word being lowercase, and should be prefixed with an underscore. This is consistent with instance variables synthesized automatically by LLVM. **If LLVM can synthesize the variable automatically, then let it.**

**Example:**

```objc
@synthesize descriptiveVariableName = _descriptiveVariableName;
```

**Not:**

```objc
id varnm;
```

---

If you do need to declare an instance variable, you should prefix it with an underscore as in the case of private methods.

## Comments and Documentation

When they are needed, comments should be used to explain **why** a particular piece of code does something. Any comments that are used must be kept up-to-date or deleted.

Block comments should generally be avoided, as code should be as self-documenting as possible, with only the need for intermittent, few-line explanations. This does not apply to those comments used to generate documentation.

Always group methods into functional groups using `#pragma mark -` as a separator. All delegate methods and protocol implementations should be categorized this way as well as View Lifecycle, etc.

## init and dealloc

`dealloc` methods should be placed at the top of the implementation, directly after the `@synthesize` and `@dynamic` statements. `init` should be placed directly below the `dealloc` methods of any class.

`init` methods should be structured like this:

```objc
- (instancetype)init {
  self = [super init]; // or call the designated initalizer
  if (self) {
    // Custom initialization
  }

  return self;
}
```

When allocating a new object, always use `new` in place of `alloc` and `init`. This is simply a matter of being terse.

**Example:**

```objc
DMSActivity *activity = [DMSActivity new];
```

**Not:**

```objc
DMSActivity *activity = [[DMSActivity alloc] init];
```

Custom init methods, singletons, or any method that returns an instance of the class should use the return type of `instancetype` rather than `id`.

## Literals

`NSString`, `NSDictionary`, `NSArray`, and `NSNumber` literals should be used whenever creating immutable instances of those objects. Pay special care that `nil` values not be passed into `NSArray` and `NSDictionary` literals, as this will cause a crash.

**Example:**

```objc
NSArray *names = @[@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul"];
NSDictionary *productManagers = @{@"iPhone" : @"Kate", @"iPad" : @"Kamal", @"Mobile Web" : @"Bill"};
NSNumber *shouldUseLiterals = @YES;
NSNumber *buildingZIPCode = @10018;
```

**Not:**

```objc
NSArray *names = [NSArray arrayWithObjects:@"Brian", @"Matt", @"Chris", @"Alex", @"Steve", @"Paul", nil];
NSDictionary *productManagers = [NSDictionary dictionaryWithObjectsAndKeys: @"Kate", @"iPhone", @"Kamal", @"iPad", @"Bill", @"Mobile Web", nil];
NSNumber *shouldUseLiterals = [NSNumber numberWithBool:YES];
NSNumber *buildingZIPCode = [NSNumber numberWithInteger:10018];
```

## CGRect Functions

When accessing the `x`, `y`, `width`, or `height` of a `CGRect`, always use the [`CGGeometry` functions](http://developer.apple.com/library/ios/#documentation/graphicsimaging/reference/CGGeometry/Reference/reference.html) instead of direct struct member access. From Apple's `CGGeometry` reference:

> All functions described in this reference that take CGRect data structures as inputs implicitly standardize those rectangles before calculating their results. For this reason, your applications should avoid directly reading and writing the data stored in the CGRect data structure. Instead, use the functions described here to manipulate rectangles and to retrieve their characteristics.

**Example:**

```objc
CGRect frame = self.view.frame;

CGFloat x = CGRectGetMinX(frame);
CGFloat y = CGRectGetMinY(frame);
CGFloat width = CGRectGetWidth(frame);
CGFloat height = CGRectGetHeight(frame);
```

**Not:**

```objc
CGRect frame = self.view.frame;

CGFloat x = frame.origin.x;
CGFloat y = frame.origin.y;
CGFloat width = frame.size.width;
CGFloat height = frame.size.height;
```

## Constants

Constants are preferred over in-line string literals or numbers, as they allow for easy reproduction of commonly used variables and can be quickly changed without the need for find and replace. Constants should be declared as `static` constants and not `#define`s unless explicitly being used as a macro. You should always prepend the constant variable name with `k`.

**Example:**

```objc
static NSString * const kAboutViewControllerCompanyName = @"The New York Times Company";

static const CGFloat kImageThumbnailHeight = 50.0;
```

**Not:**

```objc
#define CompanyName @"The New York Times Company"

#define thumbnailHeight 2
```

---

You only need to add the namespace to a constant that is made public. For example, constants used within an implementation should follow this pattern:

`static NSString * const kShowProfileSegueIdentifier = @"showProfile";`

Constants exposed externally should use this pattern in the interface:

`extern NSString *const kDMSAnalyticsCreatedEntry;`

and this pattern in the implementation file:

`NSString * const kDMSAnalyticsCreatedEntry = @"createdEntry";`

## Enumerated Types

When using `enum`s, it is recommended to use the new fixed underlying type specification because it has stronger type checking and code completion. The SDK now includes a macro to facilitate and encourage use of fixed underlying types — `NS_ENUM()`

**Example:**

```objc
typedef NS_ENUM(NSInteger, DMSAdRequestState) {
  DMSAdRequestStateInactive,
  DMSAdRequestStateLoading
};
```

## Properties

As noted in the [Variables section](#variables), properties are always preferred to instance variables. All properties should be synthesized in the implementation with an instance variable that is prepended with an underscore.

**Example:**

```objc
@synthesize buttonView = _buttonView;
```

Always access properties using `self` rather than the instance variable in order to call the accessor and setter methods.

Property attributes should always be declared, even the default values to make them explicit, and in this specific order:

```objc
@property (nonatomic, strong, readonly) DMSActivity *activity;
```

Any custom getter or setter methods should be placed at the top of the implementation file, just below the `@synthesize` statements, and above any `init` or `dealloc` methods.

### Private Properties

Private properties should be declared in class extensions (anonymous categories) in the implementation file of a class. Named categories (such as `DMSPrivate` or `private`) should never be used unless extending another class.

**Example:**

```objc
@interface DMSAdvertisement ()

@property (nonatomic, strong) GADBannerView *googleAdView;
@property (nonatomic, strong) ADBannerView *iAdView;
@property (nonatomic, strong) UIWebView *adXWebView;

@end
```

## Booleans

Since `nil` resolves to `NO` it is unnecessary to compare it in conditions. Never compare something directly to `YES`, because `YES` is defined to 1 and a `BOOL` can be up to 8 bits.

This allows for more consistency across files and greater visual clarity.

**Example:**

```objc
if (!someObject) {
}
```

**Not:**

```objc
if (someObject == nil) {
}
```

-----

**For a `BOOL`, here are two examples:**

```objc
if (isAwesome)
if (![someObject boolValue])
```

**Not:**

```objc
if ([someObject boolValue] == NO)
if (isAwesome == YES) // Never do this.
```

-----

If the name of a `BOOL` property is expressed as an adjective, the property can omit the “is” prefix but specifies the conventional name for the get accessor, for example:

```objc
@property (assign, getter=isEditable) BOOL editable;
```
Text and example taken from the [Cocoa Naming Guidelines](https://developer.apple.com/library/mac/#documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingIvarsAndTypes.html#//apple_ref/doc/uid/20001284-BAJGIIJE).

## Singletons

Singleton objects should use a thread-safe pattern for creating their shared instance.
```objc
+ (instancetype)sharedInstance {
  static id sharedInstance = nil;

  static dispatch_once_t onceToken;
  dispatch_once(&onceToken, ^{
    sharedInstance = [[self alloc] init];
  });

  return sharedInstance;
}
```
This will prevent [possible and sometimes prolific crashes](http://cocoasamurai.blogspot.com/2011/04/singletons-your-doing-them-wrong.html).

## Categories

Categories should be create for specific functionality rather than including all extensions to a Class in the same category. If you need private methods for a category, then create a category using the same name, but appended with `+Private`, eg `@interface NSDate (TimeExtensions+Private)`.

Any methods added in a named category should use a the namespace as a prefix to them.

**Example:**

```objc
@interface NSDate (TimeExtensions)
- (NSString *)dms_timeAgoShort;
@end
```

**Not:**

```objc
@interface NSDate (TimeExtensions)
- (NSString *)timeAgoShort;
@end
```

## Blocks

It's important that you don't get into a retain cycle when using blocks and asynchronous dispatch. Always set a `weak` reference to any variable referenced within a block. If you have multiple statements that use that variable within the block, you must make a `strong` reference to it from within the block in the case that it becomes `nil` after the first one.

**Example:**

```objc
__weak __typeof(self)weakSelf = self;
[self.view mas_makeConstraints:^(MASConstraintMaker *make) {
  make.edges.equalTo(weakSelf);
}];
```

**Not:**

```objc
[self.mapView mas_makeConstraints:^(MASConstraintMaker *make) {
  make.edges.equalTo(self);
}];
```

-----

**Example with multiple statements:**

```objc
__weak __typeof(self)weakSelf = self;
[self.titleLabel mas_makeConstraints:^(MASConstraintMaker *make) {
  __strong __typeof(weakSelf)strongSelf = weakSelf;
  
  make.top.equalTo(strongSelf);
  make.left.equalTo(strongSelf);
  make.right.equalTo(strongSelf);
}];
```

**Not:**

```objc
__weak __typeof(self)weakSelf = self;
[self.titleLabel mas_makeConstraints:^(MASConstraintMaker *make) {
  make.top.equalTo(weakSelf);
  make.left.equalTo(weakSelf);
  make.right.equalTo(weakSelf);
}];
```

-----

You should add these two lines as snippets to Xcode and always use them exactly like this:

```objc
__weak __typeof(self)weakSelf = self;
__strong __typeof(weakSelf)strongSelf = weakSelf;
```

You should read Apple's notes on this [here](https://developer.apple.com/library/mac/releasenotes/ObjectiveC/RN-TransitioningToARC/Introduction/Introduction.html#//apple_ref/doc/uid/TP40011226-CH1-SW9).

## Sublcassing UIView

You should always use auto-layout wherever possible. The `init` methods of a UIView should only contain a call to `[self _setupView]` in their implementation.

**Example:**

```objc
- (id)initWithFrame:(CGRect)frame {
  self = [super initWithFrame:frame];
  if(self) {
    [self _setupView];
  }
  return self;
}
```

Your `_setupView` method should call an `_applyConstraints` method at the bottom to set up any constraints it needs for the view.

You should use the setter methods for any properties that change how your view is displayed to update it before calling `setNeedsUpdateConstraints` or `setNeedsLayout` as necessary.

## Xcode project

The physical files should be kept in sync with the Xcode project files in order to avoid file sprawl. Any Xcode groups created should be reflected by folders in the filesystem. Code should be grouped not only by type, but also by feature for greater clarity.

When possible, always turn on "Treat Warnings as Errors" in the target's Build Settings and enable as many [additional warnings](http://boredzo.org/blog/archives/2009-11-07/warnings) as possible. If you need to ignore a specific warning, use [Clang's pragma feature](http://clang.llvm.org/docs/UsersManual.html#controlling-diagnostics-via-pragmas).

# Other Objective-C Style Guides

If ours doesn't fit your tastes, have a look at some other style guides:

* [Google](http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml)
* [GitHub](https://github.com/github/objective-c-conventions)
* [Adium](https://trac.adium.im/wiki/CodingStyle)
* [Sam Soffes](https://gist.github.com/soffes/812796)
* [CocoaDevCentral](http://cocoadevcentral.com/articles/000082.php)
* [Luke Redpath](http://lukeredpath.co.uk/blog/my-objective-c-style-guide.html)
* [Marcus Zarra](http://www.cimgf.com/zds-code-style-guide/)
