---
layout: post
title: 贪吃蛇、FlappyBird遇上C语言
tags: C、CPP
---

![C](/images/c.jpg)

自从学习了《C语言程序设计》课程，我开始感受到编程的魅力。C语言相对其他多数编程语言来说比较“低级”，主要被应用于编写操作系统或者驱动开发，初学者学习完之后，很难通过自己动手写一些”小玩意“获得成就感，因此学习的过程也比较枯燥。

我学完之后也在想：除了“货物价格计算”、”学生成绩管理系统“，我还能用C语言做些什么？甚至在很长一段时间，我都想不明白那些窗口程序是如何开发出来的。记得老师上课时讲他大学学完C语言七天写出贪吃蛇，如今互联网时代，资料查找方便多了，我花了四天时间，也写了个贪吃蛇。

## 贪吃蛇

我的思路很简单，C语言不是有个`printf()`函数嘛，我需要什么画面就用它打印出来！
首先，开辟一个`square[M][N]`二维数组，其中每个元素都代表一个“像素点”：

- 元素值默认为`0`，表示空格；
- 元素值修改为`1`时 ，表示围墙；
- 元素值修改为`2`时 ，表示蛇；
- 元素值修改为`3`时 ，表示食物。

用`Draw()`函数打印：

    void Draw()                        //输出图形
    {
      int i,j;
      for(i = 0; i <= M-1 ; i ++)
        for(j = 0; j <= N-1 ; j ++){
          if(square[i][j] == 1)
            printf(" *");
          else if(square[i][j] == 2)
            printf(" @");
          else if(square[i][j] == 3)
            printf(" $");
          else
            printf("  ");
          if(j == N-1 ){
            switch(i){
              case  3 : printf("       贪吃蛇\n");        break ;
              case  4 : printf("     @iH8ra1n\n");       break ;
              case  7 : printf("       总得分\n");        break ;
              case  9 : printf("         %d\n",score);        break ;
              case 11 : printf("        难度\n");        break ;
              case 13 : printf("         %d\n",difficulty);        break ;
              case 17 : printf("    ↑\n");               break ;
              case 18 : printf("  ←  →  WADS控制\n");   break ;
              case 19 : printf("    ↓\n");               break ;
              case 21 : printf("   任意键开始游戏\n");    break ;
              case 15 : if(flag == 1){
                          system("color 47");
                          printf("     GAME OVER !\n");   break ;
                        }
              default : printf("\n");
          }
        }
      }
    }

这样，就有了贪吃蛇的基本界面：

![snake.png](/images/snake.png)

太棒了！接下来要做的就是对这些“像素点”赋值，“画”出一条蛇，还要随机地在某个点出现食物。
用结构体定义蛇结点，蛇是会移动的，每一次移动，蛇身的第一节向前移动一个位置，第二节移动到第一节的位置，依此类推...所以在结构体内，要让每个结点带上指向前一节和后一节的指针。后来学了《数据结构》我才知道，原来那就是双向链表=_=#。

    typedef struct Snake
    {
	    int x,y;                       //snake的坐标
	    struct Snake *prior;           //前驱指针
	    struct Snake *next;            //后继指针
    }snake;

再简化一下，我们是通过改变`square[M][N]`的元素值让`Draw()`函数绘制游戏画面的，因此只需在蛇的第一节前面（或者左边代表左转，右边代表右转）添加一个结点，再删除最后一个结点，蛇就能移动了。随机选点显示食物，检测键盘输入，`Move()`函数实现键盘对蛇转向的控制，并且加入碰撞检测和蛇的死亡条件，最后再加入分数、提示音、难度（通过控制`sleep()`时间实现）等细节。

    int Move(){                       //蛇的移动
      int a = head->x;
      int b = head->y;
      switch(direction){              //检测键盘输入的direction
      case UP:    --a; break;
      case DOWN:  ++a; break;
      case RIGHT: ++b; break;
      case LEFT:  --b; break;
      }
      if(square[a][b] == 1 || square[a][b] == 2 ){        //碰撞检测
        flag = 1;
        Draw();
        printf("\a\a");
        system("pause");
        flag2 = 1;
        return flag2;
      }
      if(square[a][b] == 3 ){        //吃到食物
        AddHead(a,b);
        printf("\a");
        CreateFood();
        score += difficulty;
        return;
      }
      AddHead(a,b);          //添加头结点
      DeleteTail();          //删除尾结点
      return 0;
    }

一个完整的贪吃蛇游戏就写好了，试玩一下：

![snake.gif](/images/snake.gif)

## Flappy Bird

最近移动端的一款叫做"Flappy Bird"的游戏火得一塌糊涂，游戏设计和玩法十分简单，但几乎“自虐”的难度让玩家根本停不下来。游戏逻辑正好跟我之前写的C语言贪吃蛇类似，因此我决定用C语言写一个控制台版本的Flappy Bird。思路和贪吃蛇差不多，不同之处是小鸟始终在同一横向坐标，仅在纵向上下移动；而水管口高度随机，并且横向移动。

![flappybird.png](/images/flappybird.png)

那如何产生高度不同的水管呢？其实水管的形状都是一样的，只是开口的高度不一样，随机产生一个点，将这个点作为水管开口的最左端，完整的水管就可以推算出来了。

    void Create_Pipe(int *r){
        int i;
        switch(flag3%10){        //flag3列计数，用于判断是否应该产生水管
        case 1:
            srand(time(NULL));
            *r = rand() % 8 + 4;        //随机产生水管开口的坐标
            game[*r][N-3] = 1;
            game[*r+7][N-3] = 1; break;
        case 2:
        case 3:
        case 4:
            for( i = *r ; i >= 1 ; i -- )
                game[i][N-3] = 1;
            for( i = *r+7 ; i <= M-2 ; i ++ )
                game[i][N-3] = 1; break;
        case 5:
            game[*r][N-3] = 1;
            game[*r+7][N-3] = 1; break;
        default:
            break;
        }
        if(flag3 == 21 || (flag3 > 21 && (flag3 - 21)%10 == 0)){    //刷新得分
            score ++;
            printf("\a");
        }
    }

游戏截图：

![flappybird.gif](/images/flappybird.gif)

源代码下载：[Snake&Flappy_Bird.zip](/images/Snake&Flappy_Bird.zip)，代码在`Visual C++ 6.0`编译通过，使用了Windows的头文件，因此仅能在Windows环境下执行。

直接下载编译好的二进制文件：

- [Snake.exe](/images/Snake.exe)
- [Flappy_bird.exe](/images/Flappy_bird.exe)
