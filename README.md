# 将AI应用数独

Created: Mar 31, 2020 1:35 PM

## 0.1 介绍


## 0.2 解决数独

数独是世界上最受欢迎的难题之一，它由9x9的网格组成，目标是用数字填充网格，使每一行，每一列以及9个主要的3x3子正方形中的每一个都包含1到9的数字。

难题以部分完成的网格形式给出，目标是填写缺失的数字。 以下是此类网格的示例。

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082917.png)

### 项目的目标

该项目的主要目标是构建一个智能代理，该智能代理将解决每个数独问题，同时向您介绍在整个AI领域中使用的两种强大技术：

**约束传播**

当你尝试解决问题时，你会发现每个正方形都有一些局部约束。这些约束条件可以帮你缩小答案的范围，这可能会很有帮助。我们将要学习从这些约束条件中提取最大的信息，以便更接近我们的解决方案。此外，你将看到我们如何反复应用简单的约束条件来迭代的缩小解决方案的搜索空间。约束传播可用于解决各种问题，例如日历计划和密码难题。

**搜索**

在解决问题的过程中，我们可能会找到两种或者多种的可能的解决路径，我们该怎么做？如果我们分两条路考虑他们，该怎么办？也许其中一条路径可能会产生三种或者三种以上的可能性。然后我们再进行分支。最后，我们可以创建一整套可能树，并找到遍历树的方法，知道找到解决方案为止。这是如何使用搜索的示例。

这些想法可能看起来很简单，实际上是预期的！通过本课程，您将看到AI是如何真正由非常简单的想法组成的，这些想法可以组合起来解决复杂的问题。在本课程中，我们向你提出挑战，请你思考如何应用这些思想来构建AI代理，以解决您世界中的其他难题和问题！

## 0.3 建立模板

**数独：解决方案**

你的解决方案包含以下两个步骤：

- 如果一个空格有一个数值，则同一行，同一列，同一个3x3的方格中的所有框都不能有相同的值。
- 如果行、列、3x3的方格只有一个可能的值，那该框就是这个值。

### 命名标记

**行和列**

由于我们正在编写用于解决Sudoku难题的代理程序，因此让我们从标记行和列开始。

- 行用字母A，B，C，D，E，F，G，H，I标记。
- 列用数字1、2、3、4、5、6、7、8、9标记。
- 在这里，我们可以看到带有行和列标签的未解决和已解决的难题。 3x3正方形不会被标记，但是在图中，可以看到它们具有灰色和白色的交替颜色。

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082900.png)

### 盒子、单元、对等

让我们开始命名由这些行和列创建的与解决Sudoku相关的重要元素：

- 行和列相交处的各个正方形称为框。 这些框将带有标签“ A1”，“ A2”，…，“ I9”。
- 完整的行，列和3x3的正方形称为单位。 因此，每个单元是一组9个盒子，总共有27个单元。
- 对于特定的盒子（例如“ A1”），其对等方将是属于同一单位的所有其他盒子（即，属于同一行，列或3x3正方形的盒子）。

让我们来看一个例子。 在下面的网格中，突出显示的框集代表单位。 每个网格在E3处显示了框的不同对等点。

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082908.png)

## 0.4 编码

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082909.png)

为了实现程序，我们开始使用Python编写代码。然后，我们将编写解决Sudoku的必要功能的代码。我们将以两种方式记录谜题-字符串和字典。

字符串将由行中所有的数字串联组成，从上到下至各行。如果未知的数字，我们就用一个空的占位符替代。

例如，左上方未解决的难题将写为：

**..3.2.6..9..3.5..1..18.64 .... 81.29..7 ....... 8..67.82 .... 26.95..8..2.3..9 ..5.1.3 ..**

右上方已解决的难题将记录为：
**483921657967345821251876493548132976729564138136798245372689514814253769695417382**

我们将按以下方式实现字典。键将是对应于框的字符串，即“ A1”，“ A2”，…，“ I9”。值将是每个框中的数字（如果有）或“.” （如果不）。

因此，让我们开始吧。 首先，我们将行和列记录为字符串。

    rows = 'ABCDEFGHI'
    cols = '123456789'

我们将从编写一个辅助函数cross（a，b）开始，给定两个字符串a和b，该函数将返回由字符串a中的字母s和字符串t中的所有可能串联构成的列表 b。

因此cross（'abc'，'def'）将返回['ad'，'ae'，'af'，'bd'，'be'，'bf'，'cd'，'ce'，'cf'] 。

    def cross(a, b):
    				return [s+t for s in a for t in b]	

def cross（a，b）： 返回[s + t表示s在a中在b中在t中]

现在，要创建盒子的标签

    boxes = cross(rows, cols)
    boxes =
        ['A1', 'A2', 'A3', 'A4', 'A5', 'A6', 'A7', 'A8', 'A9',
         'B1', 'B2', 'B3', 'B4', 'B5', 'B6', 'B7', 'B8', 'B9',
         'C1', 'C2', 'C3', 'C4', 'C5', 'C6', 'C7', 'C8', 'C9',
         'D1', 'D2', 'D3', 'D4', 'D5', 'D6', 'D7', 'D8', 'D9',
         'E1', 'E2', 'E3', 'E4', 'E5', 'E6', 'E7', 'E8', 'E9',
         'F1', 'F2', 'F3', 'F4', 'F5', 'F6', 'F7', 'F8', 'F9',
         'G1', 'G2', 'G3', 'G4', 'G5', 'G6', 'G7', 'G8', 'G9',
         'H1', 'H2', 'H3', 'H4', 'H5', 'H6', 'H7', 'H8', 'H9',
         'I1', 'I2', 'I3', 'I4', 'I5', 'I6', 'I7', 'I8', 'I9']

对应的单元：

    row_units = [cross(r, cols) for r in rows]
    # Element example:
    # row_units[0] = ['A1', 'A2', 'A3', 'A4', 'A5', 'A6', 'A7', 'A8', 'A9']
    # This is the top most row.
    
    column_units = [cross(rows, c) for c in cols]
    # Element example:
    # column_units[0] = ['A1', 'B1', 'C1', 'D1', 'E1', 'F1', 'G1', 'H1', 'I1']
    # This is the left most column.
    
    square_units = [cross(rs, cs) for rs in ('ABC','DEF','GHI') for cs in ('123','456','789')]
    # Element example:
    # square_units[0] = ['A1', 'A2', 'A3', 'B1', 'B2', 'B3', 'C1', 'C2', 'C3']
    # This is the top left square.
    
    unitlist = row_units + column_units + square_units

现在，我们准备将数独的字符串表示形式转换为字典表示形式。

实现grid_values（）
将拼图的字符串表示形式转换为字典形式的函数。

***'..3.2.6..9..3.5..1..18.64....81.29..7.......8..67.82....26.95..8..2.3..9..5.1.3..'***

转化为字典：

    {
      'A1': '.'
      'A2': '.',
      'A3': '3',
      'A4': '.',
      'A5': '2',
      ...
      'I9': '.'
    }

实现一个名为grid_values（）的函数来执行此任务。

以下是正确实现此功能时应看到的示例。 display（）函数可以很好地直观显示字典，并且已在utils.py中提供。

    rows = 'ABCDEFGHI'
    cols = '123456789'
    
    def cross(a, b):
        return [s+t for s in a for t in b]
    
    boxes = cross(rows, cols)
    
    row_units = [cross(r, cols) for r in rows]
    column_units = [cross(rows, c) for c in cols]
    square_units = [cross(rs, cs) for rs in ('ABC','DEF','GHI') for cs in ('123','456','789')]
    unitlist = row_units + column_units + square_units
    units = dict((s, [u for u in unitlist if s in u]) for s in boxes)
    peers = dict((s, set(sum(units[s],[]))-set([s])) for s in boxes)
    
    def display(values):
        """
        Display the values as a 2-D grid.
        Input: The sudoku in dictionary form
        Output: None
        """
        width = 1+max(len(values[s]) for s in boxes)
        line = '+'.join(['-'*(width*3)]*3)
        for r in rows:
            print(''.join(values[r+c].center(width)+('|' if c in '36' else '')
                          for c in cols))
            if r in 'CF': print(line)
        return
    
    def grid_values(grid):
        """Convert grid string into {<box>: <value>} dict with '.' value for empties.
    
        Args:
            grid: Sudoku grid in string form, 81 characters long
        Returns:
            Sudoku grid in dictionary form:
            - keys: Box labels, e.g. 'A1'
            - values: Value in corresponding box, e.g. '8', or '.' if it is empty.
        """
        
        assert len(grid) == 81, "Input grid must be a string of length 81 (9x9)"
        return dict(zip(boxes, grid))

    >>> from utils import display
    >>> display(grid_values('..3.2.6..9..3.5..1..18.64....81.29..7.......8..67.82....26.95..8..2.3..9..5.1.3..'))
    . . 3 |. 2 . |6 . . 
    9 . . |3 . 5 |. . 1 
    . . 1 |8 . 6 |4 . . 
    ------+------+------
    . . 8 |1 . 2 |9 . . 
    7 . . |. . . |. . 8 
    . . 6 |7 . 8 |2 . . 
    ------+------+------
    . . 2 |6 . 9 |5 . . 
    8 . . |2 . 3 |. . 9 
    . . 5 |. 1 . |3 . .

## 0.5 策略1：消除

首先，让我们看一个盒子并分析其中可能的值。

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082911.png)

红色框可能的值是4和7。我们如何解决这个问题？ 好吧，所有其他值已经出现在突出显示框的同一列，行或3x3正方形中，因此我们得出结论，它们不能是此框的值。 换句话说，我们使用以下策略。

如果为框分配了值，则该框的所有对等方都不能具有该值

我们可以通过一次检查，遍历每个具有值的框，并根据其对等点消除框上无法显示的值。 完成后，将如下所示（为清楚起见，我们以粗体突出显示了原始填充框）：

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082910.png)

这似乎是我们可以编码的东西！改进形grid_values（）

截至目前，我们以字典形式记录难题，其中的key是盒子（'A1'，'A2'，...，'I9'），值是每个盒子的值（如果有值存在）或“.” （如果该框尚未分配值）。 我们真正想要的是让每个值代表该框的所有可用值。 例如，上方第二行和第五列中的框将具有键“ B5”和值“ 47”（因为4和7是唯一可能的值）。 因此，每个空框的起始值为'123456789'。

更新grid_values（）函数以返回“ 123456789”而不是“.”用于空盒子。

    def grid_values(grid):
        """Convert grid string into {<box>: <value>} dict with '123456789' value for empties.
    
        Args:
            grid: Sudoku grid in string form, 81 characters long
        Returns:
            Sudoku grid in dictionary form:
            - keys: Box labels, e.g. 'A1'
            - values: Value in corresponding box, e.g. '8', or '123456789' if it is empty.
        """
        values = []
        all_digits = '123456789'
        for c in grid:
            if c == '.':
                values.append(all_digits)
            elif c in all_digits:
                values.append(c)
        assert len(values) == 81
        return dict(zip(boxes, values))

因此，从现在开始，我们将以这种方式考虑难题。 因此，上一节中的难题将如下所示（以字典形式）：

    {
        'A1': '123456789',
        'A2': '123456789',
        'A3': '3',
        'A4': '123456789'
        'A5': '2',
        ...
        'I9': '123456789'
    }

### 实现eliminate()

现在，让我们完成函数exclude（）的代码，该代码将以字典形式输入一个难题。 该函数将遍历拼图中仅分配了一个值的所有框，并且将从每个对等方中删除该值。

    def eliminate(values):
        """Eliminate values from peers of each box with a single value.
    
        Go through all the boxes, and whenever there is a box with a single value,
        eliminate this value from the set of values of all its peers.
    
        Args:
            values: Sudoku in dictionary form.
        Returns:
            Resulting Sudoku in dictionary form after eliminating values.
        """
        solved_values = [box for box in values.keys() if len(values[box]) == 1]
        for box in solved_values:
            digit = values[box]
            for peer in peers[box]:
                values[peer] = values[peer].replace(digit,'')
        return values

## 0.6 策略2：唯一的值

如果单位中只有一个允许输入特定数字的框，则必须为该框分配该数字。

是时候编写代码了！ 在下一个测验中，完成函数only_choice的代码，该代码将以字典形式输入一个谜题。 该功能将遍历所有单位，如果某个单位的数字仅适合一个可能的框，它将将该数字分配给该框。

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082912.png)

    def only_choice(values):
        """Finalize all values that are the only choice for a unit.
    
        Go through all the units, and whenever there is a unit with a value
        that only fits in one box, assign the value to this box.
    
        Input: Sudoku in dictionary form.
        Output: Resulting Sudoku in dictionary form after filling in only choices.
        """
        # TODO: Implement only choice strategy here
        for unit in unitlist:
            for digit in '123456789':
                dplaces = [box for box in unit if digit in values[box]]
                    if len(dplaces) == 1:
                        values[dplaces[0]] = digit
        return values

## 0.7 约束传播

如果到目前为止，您已经接触到了AI的强大技术-约束传播。 约束传播是关于使用空间中的局部约束（对于Sudoku，是每个正方形的约束）来极大地减少搜索空间。 当我们执行每个约束时，我们将看到它如何为其他部分引入新的约束，这可以帮助我们进一步减少可能性。 我们有一整节课专门讨论约束传播，但让我们快速了解一下它可以帮助我们解决的其他一些著名的AI问题。

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082913.png)

现在您了解了如何将约束传播应用于此问题，让我们尝试对其进行编码！ 在以下测验中，将eliminate和only_choice函数结合在一起以编写reduce_puzzle函数，该函数接收未解决的难题作为输入，并反复应用我们的两个约束以尝试解决该难题。

需要注意的一些事项：

- 如果难题得到解决，则该功能需要停止。 这个怎么做？
- 如果函数不能解决数独怎么办？ 当应用这两种策略停止进展时，我们是否可以确保函数退出？

    def reduce_puzzle(values):
        """
        Iterate eliminate() and only_choice(). If at some point, there is a box with no available values, return False.
        If the sudoku is solved, return the sudoku.
        If after an iteration of both functions, the sudoku remains the same, return the sudoku.
        Input: A sudoku in dictionary form.
        Output: The resulting sudoku in dictionary form.
        """
        stalled = False
        while not stalled:
            # Check how many boxes have a determined value
            solved_values_before = len([box for box in values.keys() if len(values[box]) == 1])
            # Use the Eliminate Strategy
            values = eliminate(values)
            # Use the Only Choice Strategy
            values = only_choice(values)
            # Check how many boxes have a determined value, to compare
            solved_values_after = len([box for box in values.keys() if len(values[box]) == 1])
            # If no new values were added, stop the loop.
            stalled = solved_values_before == solved_values_after
            # Sanity check, return False if there is a box with zero available values:
            if len([box for box in values.keys() if len(values[box]) == 0]):
                return False
        return values

## 0.8 高难度数独

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082914.png)

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082916.png)

不好了！ 该算法无法解决。 它似乎使每个盒子都减少了许多可能性，但是它不会比这更进一步。 我们需要考虑其他方法来改进我们的解决方案。

## 0.9 策略3：搜索

现在，我们将使用另一种基础AI技术来帮助我们解决此问题：搜索。

从游戏玩法到路线规划，整个AI都使用搜索来有效地找到解决方案。

这就是我们将如何应用它。 框“ A2”有四个可能性：1、6、7和9。为什么我们不给它填充1并尝试解决难题。 如果我们不能解决问题，我们将尝试使用6，然后尝试7，然后使用9。当然，这是工作量的四倍，但是每种情况都会变得更容易。

实际上，还有一些比这更聪明的事情。 仔细看拼图，有没有比“ A2”更好的选择了？

选择一个具有尽可能少的可能值的框。 尝试递归地选择每个值来解决每个难题。

在深入编写搜索功能代码之前，让我们首先检查一下我们的理解。 您将如何使用“深度优先搜索”遍历以下树？

Depth First Search.

## 10. 编码解决方案

    import pdb
    
    rows = 'ABCDEFGHI'
    cols = '123456789'
    
    def cross(a, b):
        return [s+t for s in a for t in b]
    
    
    boxes = cross(rows, cols)
    
    row_units = [cross(r, cols) for r in rows]
    column_units = [cross(rows, c) for c in cols]
    square_units = [cross(rs, cs) for rs in ('ABC','DEF','GHI') for cs in ('123','456','789')]
    unitlist = row_units + column_units + square_units
    units = dict((s, [u for u in unitlist if s in u]) for s in boxes)
    peers = dict((s, set(sum(units[s],[]))-set([s])) for s in boxes)
    
    
    def display(values):
        """
        Display the values as a 2-D grid.
        Input: The sudoku in dictionary form
        Output: None
        """
        width = 1+max(len(values[s]) for s in boxes)
        line = '+'.join(['-'*(width*3)]*3)
        for r in rows:
            print(''.join(values[r+c].center(width)+('|' if c in '36' else '')
                          for c in cols))
            if r in 'CF': print(line)
        return
    
    
    # WARNING! We've modified this function to return '123456789' instead of '.' for boxes with no value.
    # Look at the explanation above in the text.
    def grid_values(grid):
        """Convert grid string into {<box>: <value>} dict with '123456789' value for empties.
    
        Args:
            grid: Sudoku grid in string form, 81 characters long
        Returns:
            Sudoku grid in dictionary form:
            - keys: Box labels, e.g. 'A1'
            - values: Value in corresponding box, e.g. '8', or '123456789' if it is empty.
        """
        values = []
        all_digits = '123456789'
        for c in grid:
            if c == '.':
                values.append(all_digits)
            elif c in all_digits:
                values.append(c)
        assert len(values) == 81
        return dict(zip(boxes, values))
    
    
    def eliminate(values):
        """Eliminate values from peers of each box with a single value.
    
        Go through all the boxes, and whenever there is a box with a single value,
        eliminate this value from the set of values of all its peers.
    
        Args:
            values: Sudoku in dictionary form.
        Returns:
            Resulting Sudoku in dictionary form after eliminating values.
        """
        solved_values = [box for box in values.keys() if len(values[box]) == 1]
        for box in solved_values:
            digit = values[box]
            for peer in peers[box]:
                values[peer] = values[peer].replace(digit,'')
        return values
    
    
    def only_choice(values):
        """Finalize all values that are the only choice for a unit.
    
        Go through all the units, and whenever there is a unit with a value
        that only fits in one box, assign the value to this box.
    
        Input: Sudoku in dictionary form.
        Output: Resulting Sudoku in dictionary form after filling in only choices.
        """
        # TODO: Implement only choice strategy here
        for unit in unitlist:
            for digit in '123456789':
                dplaces = [box for box in unit if digit in values[box]]
                if len(dplaces) == 1:
                    values[dplaces[0]] = digit
        return values
    
    def reduce_puzzle(values):
        """
        Iterate eliminate() and only_choice(). If at some point, there is a box with no available values, return False.
        If the sudoku is solved, return the sudoku.
        If after an iteration of both functions, the sudoku remains the same, return the sudoku.
        Input: A sudoku in dictionary form.
        Output: The resulting sudoku in dictionary form.
        """
        stalled = False
        while not stalled:
            # Check how many boxes have a determined value
            solved_values_before = len([box for box in values.keys() if len(values[box]) == 1])
            # Use the Eliminate Strategy
            values = eliminate(values)
            # Use the Only Choice Strategy
            values = only_choice(values)
            # Check how many boxes have a determined value, to compare
            solved_values_after = len([box for box in values.keys() if len(values[box]) == 1])
            # If no new values were added, stop the loop.
            stalled = solved_values_before == solved_values_after
            # Sanity check, return False if there is a box with zero available values:
            if len([box for box in values.keys() if len(values[box]) == 0]):
                return False
        return values
    
    def search(values):
        "Using depth-first search and propagation, create a search tree and solve the sudoku."
        # First, reduce the puzzle using the previous function
        values = reduce_puzzle(values)
        if values is False:
            return False ## Failed earlier
        if all(len(values[s]) == 1 for s in boxes): 
            return values ## Solved!
        # Choose one of the unfilled squares with the fewest possibilities
        n,s = min((len(values[s]), s) for s in boxes if len(values[s]) > 1)
        # Now use recursion to solve each one of the resulting sudokus, and if one returns a value (not False), return that answer!
        for value in values[s]:
            new_sudoku = values.copy()
            new_sudoku[s] = value
            attempt = search(new_sudoku)
            if attempt:
                return attempt
        # If you're stuck, see the solution.py tab!
        
    
    test = '..3.2.6..9..3.5..1..18.64....81.29..7.......8..67.82....26.95..8..2.3..9..5.1.3..'
    
    grid2 = '4.....8.5.3..........7......2.....6.....8.4......1.......6.3.7.5..2.....1.4......'
    
    
    test1 = search(grid_values(grid2))
    display(test1)

![](https://raw.githubusercontent.com/450703035/picgo/master/20200410082915.png)