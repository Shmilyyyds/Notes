# 软工第二次作业报告

## 概述列表
|分类|任务概述|起止时间|
|-|-|-|
|分析|阅读相关资料，初步得出思路|视频没录上|
|项目框架构建|配置项目，构建项目框架|0:00~02:01|
|第一段编码|书写输入输出函数，以及`possibleNumberInference`函数|02:01~17:21|
|第二段编码|书写`lastRemainingCellInference`函数，但失败|17:21~22:51|
|调试|尝试调试解决，但由于时间原因未能完成|22:51~28:55|


## 详情
### 分析
理解需求，了解题目要求，阅读相关资料，初步得出思路。

### 项目框架构建
项目结构如下，采用**Java**语言编写，使用**Maven**构建项目。
```
src/
├── main/
│   ├── java/
│   │   └── 
│   │      ├── SudokuSolver.java
│   │      ├── inference/
│   │      │   ├── LastRemainingCellInference.java
│   │      │   └── PossibleNumberInference.java
│   │      └── util/
│   │          └── SudokuPrinter.java
│   └── resources/
└── test/
    └── java/
        └── SudokuSolverTest.java
```

### 第一段编码
输入输出函数略。
`possibleNumberInference`函数:
- 思路：遍历每一行、每一列、每一个3x3的宫格，找出可能的数字，并将其加入到候选列表中。
> 功能好像有点小问题。

### 第二段编码
`lastRemainingCellInference`函数：
- 思路：利用`possibleNumberInference`得到的`results`，根据所求坐标的行、列、宫格，判断该坐标是否有唯一的候选数字。
> 但是失败了，时间问题没能解决。

### 调试
尝试调试解决，但由于时间原因未能完成。



## 主要代码
```java
package inference;

import java.util.Arrays;

public class LastRemainingCellInference {

    private static final int SIZE = 9;
    private static final int SUB_SIZE = 3;

    public static int[][][] getResults(int[][] board) {
        int[][][] results = new int[SIZE][SIZE][SIZE];

        // 借助PossibleNumberInference方法
        int[][][] possibleNumberResults = PossibleNumberInference.getResults(board);

        for (int i = 0; i < SIZE; i++) {
            for (int j = 0; j < SIZE; j++) {
                if (board[i][j] != 0) {
                    results[i][j][0] = board[i][j];
                    continue;
                }

                // 初始化possibleFlag
                boolean[] possibleFlag = new boolean[SIZE + 1];
                for (int k = 0; k < SIZE; k++) {
                    if (possibleNumberResults[i][j][k] != 0) {
                        int num = possibleNumberResults[i][j][k];
                        possibleFlag[num] = true;
                    }
                }

                // 根据possibleNumberResults得到与其他关联位置比对得到的结果
                // 比对行列
                for (int k = 1; k <= SIZE; k++) {
                    if (!possibleFlag[i]) continue;
                    for (int l = 0; l < SIZE; l++) {
                        for (int m = 0; m < SIZE; m++) {
                            int checkRow = possibleNumberResults[i][l][m];
                            int checkCol = possibleNumberResults[l][j][m];
                            if (checkRow != 0 && l != j) possibleFlag[checkRow] = false;
                            if (checkCol != 0 && l != i) possibleFlag[checkCol] = false;
                        }
                    }
                }

                // 排除宫格的值
                int x_start = (i / SUB_SIZE) * SUB_SIZE;
                int y_start = (j / SUB_SIZE) * SUB_SIZE;
                for (int x = x_start; x < x_start + SUB_SIZE; x++) {
                    for (int y = y_start; y < y_start + SUB_SIZE; y++) {
                        for (int k = 0; k < SIZE; k++) {
                            if (possibleNumberResults[i][j][k] != 0) {
                                if (x != i && y != j) {
                                    possibleFlag[possibleNumberResults[x][y][k]] = false;
                                }
                            }
                        }
                    }
                }

                int idx = 0;
                for (int k = 1; k <= SIZE; k++) {
                    if (possibleFlag[i]) results[i][j][idx++] = k;
                }
            }
        }

        return results;
    }
}
```
---

```java
package inference;

import java.util.Arrays;

public class PossibleNumberInference {

    private static final int SIZE = 9;
    private static final int SUB_SIZE = 3;

    public static int[][][] getResults(int[][] board) {
        int[][][] results = new int[SIZE][SIZE][SIZE];

        // 得到results
        for (int i = 0; i < SIZE; i++) {
            for (int j = 0; j < SIZE; j++) {
                if (board[i][j] != 0) {
                    results[i][j][0] = board[i][j];
                    continue;
                }

                // 表示1-9的是否可以选择，true为可选；false为不可选
                boolean[] possibleFlag = new boolean[SIZE + 1];
                Arrays.fill(possibleFlag, true);

                // 排除行列的值
                for (int k = 0; k < SIZE; k++) {
                    if (board[i][k] != 0) possibleFlag[board[i][k]] = false;
                    if (board[k][j] != 0) possibleFlag[board[k][j]] = false;
                }

                // 排除宫格的值
                int x_start = (i / SUB_SIZE) * SUB_SIZE;
                int y_start = (j / SUB_SIZE) * SUB_SIZE;
                for (int x = x_start; x < x_start + SUB_SIZE; x++) {
                    for (int y = y_start; y < y_start + SUB_SIZE; y++) {
                        if (board[x][y] != 0) possibleFlag[board[x][y]] = false;
                    }
                }

                // 将推理结果记录进results
                int count = 0;
                for (int k = 1; k <= SIZE; k++) {
                    if (possibleFlag[k]) results[i][j][count++] = k;
                }

            }

        }
        return results;
    }

}
```