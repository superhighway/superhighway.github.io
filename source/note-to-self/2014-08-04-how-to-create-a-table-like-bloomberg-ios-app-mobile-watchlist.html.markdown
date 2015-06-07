---
title: How to Create a Table like Bloomberg iOS App's Mobile Watchlist
date: 2014-08-04
tags: iOS
---

I discovered that few hardcore stock apps users utilizes lots of numbers in a table to decide whether they need to buy or sell stocks. Oftentimes, the tables they use have plenty columns.

The Mobile Watchlist table on Bloomberg iOS app is one example solution to the horizontal spacing problem. The table can be scrolled horizontally and vertically so that the users can see more numbers. The topmost row is fixed when it needs to be, and the leftmost column is fixed.

<div>
  <div style="float:left;width:300px;margin:0 20px 0 0; text-align:center;">
    <img src="/images/2014-08-04-how-to-create-a-table-like-bloomberg-ios-app-mobile-watchlist/bloomberg-mobile-watchlist.png" alt="A Screenshot of Bloomberg Mobile Watchlist" class="no-border" />
    <p style="font-style: italic; font-size: 0.9em;">A Screenshot of Bloomberg Mobile Watchlist</p>
  </div>

  <div style="float:left;width:300px;text-align:center;">
    <a href="http://cl.ly/1O0N3h0t0s2O/mobile-watchlist-replica.mov" target="_blank" alt="A Replica of Bloomberg Mobile Watchlist">
      <img src="/images/2014-08-04-how-to-create-a-table-like-bloomberg-ios-app-mobile-watchlist/bloomberg-mobile-watchlist-replica.gif" alt="A Replica of Bloomberg Mobile Watchlist" class="no-border" />
    </a>
    <p style="font-style: italic; font-size: 0.9em;">A Replica of Bloomberg Mobile Watchlist</p>
  </div>

  <div style="clear:both;"></div>
</div>

**How can we build this kind of table in iOS? What is the overall structure?**

Essentially, this kind of table is built using `UITableView` with each cells having a `UICollectionView` that scrolls horizontally. Each time a user scrolls a cell's collection view, the other cells' collection views' content offsets are to be synchronized with that scrolling collection view. This is actually one example usage of synchronized scrolling.

![Views Breakdown](images/2014-08-04-how-to-create-a-table-like-bloomberg-ios-app-mobile-watchlist/views.png)

Just a side note, this nested `UITableView` or `UICollectionView` pattern is pretty common in finance apps and some apps in other categories.

~**Table Breakdown - Annotated**

1. Setting Up the Table
1. Building the Table Rows
1. Creating the Table Header
1. Building the Table Cells
1. The Rest of Details

### Setting Up the Table

We will use UITableView to build easily aligned labels. *If you would like to know why UITableView makes it easy to align labels, [feel free to check out this post](/2013/12/04/ways-to-display-tabular-data-on-ios/).*

1. Create an empty project
1. Create a storyboard that contains a UITableViewController
You can also use a UIViewController that has UITableView.
1. Set the storyboard as the entry point
1. Run the project to ensure that it works

### Building the Table Rows

We will build the table rows, which are actually a list of a custom UITableViewCell.

1. Create an xib of UITableViewCell that has UICollectionView in it
Make sure that labels are aligned accordingly.
1. Synchronize each collection view's content offset with the others
1. Add borders and drop shadows to the cell
1. Add dummy data (2d array) and make sure the code works

### Creating the Table Header

1. Create an xib for the table view section header that has a UICollectionView in it
Make sure that the columns alignment is the same as the cells we have created before
1. Synchronize the scrolling with the other cells
1. Add borders and drop shadows to the section header
1. Add sorting to the section header

### Building the Table Cells

1. Create an xib for the table cells
1. Add borders

### The Rest

1. Add tableHeaderView and tableFooterView
1. Add wk view

