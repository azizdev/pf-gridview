this page is to describe how to use PFGridView in your application, for more detail, please refer the demo project in the source code.

# Setup project #
You can add the source files into your project directly.

You can also make your project depend on PFGridView. In this way, need "-all\_load" and "-ObjC" in "Other Linker Flags" in the project that use it.

# Create a PFGridView #
You can either use Interface Builder to create a PFGridView in xib file, or you can use `[[PFGridView alloc] initWithFrame:frame]` to create it in code.
You need to setup some properties to use the grid properly, a proper place to do it is in the viewDidLoad() method.
The following properties are supported:

| property name | default | comment |
|:--------------|:--------|:--------|
| cellHeight | 44 | the height of each cell view, only fixed height supported now, all cells must have same height |
| header Height | 44 | the height of each header view |
| directionalLockEnabled | NO | whether enable the directional lock when scrolling in the grid |
| snapToGrid | NO | whether force the scrolling to snap to grid to always show whole cell. (not fully implemented yet) |
| snapToGridAnamationDuration | 0.1f | the duration of the animation for snapping to grid effect |
| selectMode | PFGridViewSelectModeRow | can be row mode, cell mode, or not support selecting |
| selectAnimated | YES | whether use animation when selecting cell or row |
| selectAnamationDuration | 0.2f | duration of the selection animation |

A simple example:
```
    demoGridView.cellHeight = 60.0f;
    demoGridView.headerHeight = 60.0f;
```

# PFGridIndexPath #
PFGridIndexPath is used to specify a cell, it's very simple, you can access these properties:
`section, row, col`.

# PFGridViewCell #
All the cells and headers must be subclass of PFGridViewCell, it's subclass of UIView itself, provide support for reuseIdentifier, indexPath, normal and selected background color, you probably need to create subclass for custom look and operation on them, see PFGridViewLabelCell for a quick example.

# Provide data source for it #
To show data in the grid, you have to provide data source for it, connect the data source to the grid in Interface Builder or by something like `gridView.dataSource = self;`

The following methods are required:
```
- (CGFloat)widthForSection:(NSUInteger)section;
```
Provide the width of each section.

```
- (NSUInteger)numberOfRowsInGridView:(PFGridView *)gridView;
```
How many rows are there in the grid.

```
- (NSUInteger)gridView:(PFGridView *)gridView numberOfColsInSection:(NSUInteger)section;
```
How many column there will be in each section.

```
- (CGFloat)gridView:(PFGridView *)gridView widthForColAtIndexPath:(PFGridIndexPath *)indexPath;
```
Provide the width of each column.

```
- (PFGridViewCell *)gridView:(PFGridView *)gridView cellForColAtIndexPath:(PFGridIndexPath *)indexPath;
```
This is the most important method, you need to return cell views for displaying. make sure you `dequeueReusableCellWithIdentifier: ` to reuse cells.

```
- (PFGridViewCell *)gridView:(PFGridView *)gridView headerForColAtIndexPath:(PFGridIndexPath *)indexPath;
```
This is the factory method for the header cells, make sure you use `dequeueReusableCellWithIdentifier: ` as well.

The following methods are optional
```
- (NSUInteger)numberOfSectionsInGridView:(PFGridView *)gridView;
```
How many sections will be in the grid, default as 1.

# Operations on the grid #
You can call the following methods on the grid:
```
- (void)reloadData;
```
Reload the whole grid, it will call the methods in datasource to reconstruct the grid.

```
- (PFGridViewCell *)dequeueReusableCellWithIdentifier:(NSString *)identifier;
```
Get a reusable cell (either in header of grid), for efficiently supporting massive amount of cells. Every offscreen cells will be put back into the queue, so if you don't use this properly, you will have memory issues very quickly.

```
- (PFGridViewSection *)section:(NSUInteger)sectionIndex;
```
Get the section object, this is for convenience, in case you need to add a background image to be section or something similar, please careful with this method though, since you may break its internal functionalities, and also the section can be inconsistent after reloadData.

```
- (PFGridViewCell *)cellForColAtIndexPath:(PFGridIndexPath *)indexPath;
```
You can get a specific cell, note that if the cell is not visible, you will get nil.

```
- (PFGridViewCell *)headerForColAtIndexPath:(PFGridIndexPath *)indexPath;
```
You can get a specific header , note that if the header is not visible, you will get nil.

```
- (PFGridIndexPath *)indexPathForSelectedCell;
```
Get the indexPath of the selected cell.

```
- (void)selectCellAtIndexPath:(PFGridIndexPath *)indexPath animated:(BOOL)animated scrollPosition:(PFGridViewScrollPosition)scrollPosition;
```
You can select the specific cell by calling this method, note that none delegate event will be triggered, and scrollPosition not implemented yet, only none/top supported for now.

```
- (void)scrollToCellAtIndexPath:(PFGridIndexPath *)indexPath animated:(BOOL)animated scrollPosition:(PFGridViewScrollPosition)scrollPosition;
```
You can scroll to specific cell by calling this method, note that none delegate event will be triggered, and scrollPosition not implemented yet, only none/top supported for now.


# Using delegate to get event notification #
All the following delegates are optional.

```
- (void)gridView:(PFGridView *)gridView willDisplayCell:(PFGridViewCell *)cell forColAtIndexPath:(PFGridIndexPath *)indexPath;
```
Will be called when a cell is about to be displayed, cell.indexPath and cell.frame been setup.

```
- (BOOL)gridView:(PFGridView *)gridView willSelectCellAtIndexPath:(PFGridIndexPath *)indexPath;
```
Will be called before a cell been selected, if return NO, the selection operation will be cancelled, default as YES;

```
- (void)gridView:(PFGridView *)gridView didSelectCellAtIndexPath:(PFGridIndexPath *)indexPath;
```
Will be called after a cell been selected;

```
- (BOOL)gridView:(PFGridView *)gridView willDeselectCellAtIndexPath:(PFGridIndexPath *)indexPath;
```
Will be called before a cell been deselected, if return NO, the selection operation will be cancelled, default as YES;

```
- (void)gridView:(PFGridView *)gridView didDeselectCellAtIndexPath:(PFGridIndexPath *)indexPath;
```
Will be called after a cell been deselected;

```
- (void)gridView:(PFGridView *)gridView scrollToOffsetY:(CGFloat)offsetY;
```
Will be called after user scroll on the grid, now only the offsetY value are tracked. You can use this method to synchronize other view component with the grid.