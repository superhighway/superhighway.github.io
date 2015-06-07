---
title: Ways to Display Tabular Data on iOS
date: 2013-12-04
tags: iOS
---

Recently, I work on an iOS app that helps its users to learn stock trading through simulation. I discovered that numbers, charts and tabular data are the most commonly used on the user interface. 

Well, that looks like quite a retarded discovery I've ever made...*but probably not really!*

For the past 2 years, I worked on entertainment-related web/mobile apps. Usage of tiles and images are usually very prominent for this case, compare to the stock trading app.

In stock trading and other finance-related areas, displaying numbers in tables is very important. These numbers are used by traders to decide quickly whether they have to buy, sell, or do nothing with certain stocks. Traders rely on Ms Excel sheets everyday; Excel is one of the best tools for them. Using Excel, they can make charts from tables, and many more.

Then there are users who would like to learn to be traders. They need to get acquainted with these tools too. Numbers, tables, charts...

### What are all these things have to do with displaying tabular data on iOS?

If let's say we build an iOS app for these people, we do need to make sure it somewhat simulates the tools they are already familiar with. Tables in Excel are aligned, from column to rows.

How can we code nicely aligned numbers on iOS? How can we make their alignments and values easy to change when we need to?

For example, we want to display data that looks like the following.

```


  Stock Symbol: LNKD
  Price Bought: USD 300
 Current Price: USD 300
      Quantity: 10

```

There are at least three possible approaches to implement this kind of display on iOS.

1. Using Static Labels ([Code on GitHub](http://git.io/Hbjfmg))
1. Using Outlet Collections of Labels ([Code on GitHub](http://git.io/MK5ZZw))
1. Using Table View ([Code on GitHub](http://git.io/JJsNSg))

## Using Static Labels

![Static Labels - Storyboard](2013-12-04-ways-to-display-tabular-data-on-ios/static-labels-manual-alignments.png "Static Labels - Storyboard")

With this approach, number description labels are hardcoded, and number values are bound manually. All labels' coordinates are defined on storyboards/xibs, or perhaps hardcoded on the code.

![Static Labels - Storyboard](2013-12-04-ways-to-display-tabular-data-on-ios/static-labels-hardcoded-bindings.png "Static Labels - Storyboard")

```ruby
// SLViewController.m

#import "SLViewController.h"

@interface SLViewController ()
@property (weak, nonatomic) IBOutlet UILabel *symbolLabel;
@property (weak, nonatomic) IBOutlet UILabel *priceBoughtLabel;
@property (weak, nonatomic) IBOutlet UILabel *currentPriceLabel;
@property (weak, nonatomic) IBOutlet UILabel *quantityLabel;
@end

@implementation SLViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // Hardcoded binding
    self.symbolLabel.text = @"RKUNF";
    self.priceBoughtLabel.text = @"USD 15.75";
    self.currentPriceLabel.text = @"USD 14.98";
    self.quantityLabel.text = @"100";
}

@end

```

![Static Labels - iOS Simulator](2013-12-04-ways-to-display-tabular-data-on-ios/static-labels-on-simulator.png "Static Labels - iOS Simulator")

**Pros:**

- This is the first approach that probably most iOS developers can think of. Very nice for building prototypes or quickly hack something.
- Easy to implement at first, for very small set of labels

**Cons:**

- **Aligning labels are done manually.** Wow, that's hard and tedious. As you can see from the screenshots above, the alignments don't look quite right.
- **Adding or removing labels requires aligning labels manually.** Lots of manual work here. If there is not enough space, rearranging all the labels is necessary. Try adding a new row for `Profit/Loss: -USD 77`, and see how long you spend to align the labels.
- **UI and data binding is hardcoded.** If you add `Profit/Loss: -USD 77` as a new row, most chances are that you will add two UILabel, one for `Profit/Loss:` and `-USD 77`.
- **When there are set of similar data to be displayed, there is tendency to duplicate the codes and the labels**. This is because sharing same set of data requires more typing than copying and pasting labels with existing alignment.

[Feel free to download the code from GitHub.](http://git.io/Hbjfmg)

## Using Outlet Collections of Labels

![Outlet Collections of Labels - Manual Alignments](2013-12-04-ways-to-display-tabular-data-on-ios/outlet-collections-of-labels-manual-alignments.png "Outlet Collections of Labels - Manual Alignments")

With this approach, number description labels are not hardcoded, and number values are bound dynamically. However, all the labels' positions are defined on storyboards/xibs, or perhaps hardcoded on the code. Overall, this is a slightly more dynamic approach than using static labels.

![Outlet Collections of Labels - Dynamic Bindings](2013-12-04-ways-to-display-tabular-data-on-ios/outlet-collections-of-labels-dynamic-bindings.png "Outlet Collections of Labels - Dynamic Bindings")

```ruby
// OCViewController

#import "OCViewController.h"

@interface OCViewController ()
@property (strong, nonatomic) IBOutletCollection(UILabel) NSArray *nameLabels;
@property (strong, nonatomic) IBOutletCollection(UILabel) NSArray *valueLabels;
@end

@implementation OCViewController

- (void)viewDidLoad {
    [super viewDidLoad];

    // Names and values are easily modifiable.
    NSArray *names = @[@"Symbol => ", @"Bought => ", @"Current => ", @"Qty => "];
    NSArray *values = @[@"LNKD", @"USD 350", @"USD 300", @"80"];
    for (NSInteger i = 0; i < names.count; ++i) {
        [(UILabel *)self.nameLabels[i] setText:names[i]];
        [(UILabel *)self.valueLabels[i] setText:values[i]];
    }
}

@end

```

![Outlet Collections of Labels - iOS Simulator](2013-12-04-ways-to-display-tabular-data-on-ios/outlet-collections-of-labels-on-simulator.png "Outlet Collections of Labels - iOS Simulator")

**Pros:**

- **Changing labels only requires changing the code (the name and value arrays)**; storyboards/xibs don't need to be modified.
- **Sharing similar set of row data is a little bit easier than the static labels approach.** Imagine if you have similar kind of table somewhere and you want to use the same calculations for the values.

**Cons:**

- **Aligning labels are still done manually.** Good luck with that!
- **Adding or removing labels still requires aligning labels manually.** If there is not enough space, you will need to rearrange all the labels by hand again...
- **When there are set of similar data to be displayed, there is tendency to duplicate the labels**. Notice that it's a little bit cleaner here than in the previous way; here there's less tendency to copy the codes...but duplicating the labels is still pretty bad, especially if there's a way not to do so.

[Feel free to download the code from GitHub.](http://git.io/MK5ZZw)

## Using Table View

![Table View](2013-12-04-ways-to-display-tabular-data-on-ios/table-view.png "Table View")

In this approach, we view each row as a UITableViewCell that has a left label and a right label. The left and right label data are stored in a 2d array of row name and value pairs. This is so far the most flexible way, compare to the other two approaches.

![Table View - Dynamic Bindings](2013-12-04-ways-to-display-tabular-data-on-ios/table-view-dynamic-bindings.png "Table View - Dynamic Bindings")

```ruby
// TVViewController.m

#import "TVRowTableViewCell.h"
#import "TVViewController.h"

@interface TVViewController () <
    UITableViewDataSource,
    UITableViewDelegate
>
@property (weak, nonatomic) IBOutlet UITableView *tableView;
@end

@implementation TVViewController

- (void)viewDidLoad {
    [super viewDidLoad];
    self.tableData = @[
                       @[
                         @[@"Stock Symbol:", @"LNKD"],
                         @[@"Price Bought:", @"USD 300"],
                         @[@"Current Price:", @"USD 300"],
                         @[@"Quantity:", @"10"]
                        ],
                       @[
                         @[@"Profit/Loss:", @"0"],
                         @[@"ROI:", @"0%"]
                        ]
                      ]; // dummy
}

#pragma mark - UITableViewDataSource

- (NSInteger)numberOfSectionsInTableView:(UITableView *)tableView {
    return self.tableData.count;
}

- (NSInteger)tableView:(UITableView *)tableView numberOfRowsInSection:(NSInteger)section {
    return [self.tableData[section] count];
}

- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    static NSString *cellIdentifier = @"TVRowTableViewCell";
    TVRowTableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:cellIdentifier forIndexPath:indexPath];
    [cell bindRowData:self.tableData[indexPath.section][indexPath.row]];
    return cell;
}

#pragma mark - UITableViewDelegate

- (UIView *)tableView:(UITableView *)tableView viewForFooterInSection:(NSInteger)section {
    return [[UIView alloc] initWithFrame:CGRectZero];
}

@end

```

![Table View - Row](2013-12-04-ways-to-display-tabular-data-on-ios/table-view-row.png "Table View - Row")

```ruby
// TVRowTableViewCell.m

#import "TVRowTableViewCell.h"

@implementation TVRowTableViewCell

- (void)bindRowData:(NSArray *)rowData {
    self.leftLabel.text = rowData[0];
    self.rightLabel.text = rowData[1];
}

@end

```

![Table View - iOS Simulator](2013-12-04-ways-to-display-tabular-data-on-ios/table-view-on-simulator.png "Table View - iOS Simulator")

**Pros:**

- **Align the cell once, and its alignment is applied for the rest of the table rows.** Compare this to the previous two approaches where you have to align all labels manually.
- **Adding more name value pairs only requires code change**; storyboard/xib changes are not required.
- **There is less tendency to copy-paste labels than the previous two approaches.** When you need to display this thing elsewhere, similar data -> Can share data+calculation at least
- **This approach gives the least number among the possible three approaches** if there are plenty of name value pairs (> 5).

**Cons:**

- **It takes some time to setup the table view and cell.** However, this is one-time setup for all rows, so...it's not so bad.
- **This is probably not the first approach iOS developers can think of.** This pattern is more common in JavaScript chart libraries; you "plug" 2d arrays of table data to display a fancy chart or table.

[Feel free to download the code from GitHub.](http://git.io/JJsNSg)

---

Those are the three ways I can think of. While there are pros and cons for each of the approaches, I prefer using the table view.


