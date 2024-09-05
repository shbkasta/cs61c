## 实现Game of life
```c
/************************************************************************
**
** NAME:        gameoflife.c
**
** DESCRIPTION: CS61C Fall 2020 Project 1
**
** AUTHOR:      Justin Yokota - Starter Code
**				YOUR NAME HERE
**
**
** DATE:        2020-08-23
**
**************************************************************************/

#include <stdint.h>
#include <stdio.h>
#include <stdlib.h>
#include <inttypes.h>
#include "imageloader.h"

//Determines what color the cell at the given row/col should be. This function allocates space for a new Color.
//Note that you will need to read the eight neighbors of the cell in question. The grid "wraps", so we treat the top row as adjacent to the bottom row
//and the left column as adjacent to the right column.
Color *evaluateOneCell(Image *image, int row, int col, uint32_t rule)
{
	//YOUR CODE HERE
	//取出读入图像的对应(row,col)像素
	int n=image->rows,m=image->cols; //原图像的行和列
	Color ** img=image->image;
	Color *old_pix=*(img+row*m+col);

	//对每个像素的每一位进行game of life规则转移,总共3*8=24位,相当于同时24次模拟。
	int red=0,green=0,blue=0;  //临时存储新R,G,B的值
	int dx[]={-1,-1,0,1,1,1,0,-1},dy[]={0,1,1,1,0,-1,-1,-1};
	for(int i=0;i<8;i++)
	{
		int is_alive_r=(old_pix->R>>i)&1;
		int is_alive_g=(old_pix->G>>i)&1;
		int is_alive_b=(old_pix->B>>i)&1;
		
		int count_r=0,count_g=0,count_b=0;
		for(int i=0;i<8;i++)
		{
			int x=(row+dx[i]+n)%n;  //项目要求环状
			int y=(col+dy[i]+m)%m;
			Color *new_pix=*(img+x*m+y);
			if(new_pix->R&(1<<i)) count_r++;
			if(new_pix->G&(1<<i)) count_g++;
			if(new_pix->B&(1<<i)) count_b++;
		}

		//rule 的低9位代表当前状态为死亡，下一轮附近需要几个存活才存活，
		//比如00000100表示需要附近有2个存活，下一轮存活
		//高9位表示当前状态位存活，下一轮附近几个存活下一轮存活
		//比如00001100表示附近有两个或者3个存活，下一轮存活
		//因此game of life游戏规则是0x1808
		int r=is_alive_r*9+count_r;
		int g=is_alive_g*9+count_g;
		int b=is_alive_b*9+count_b;
		//表示按照规则，其下一轮是否存活，如果存活，则第i位为1，否则为0；
		red+=(1&(rule>>r))<<i; 
		green+=(1&(rule>>g))<<i;
		blue+=(1&(rule>>b))<<i;
		
	}
	//申请一个像素的空间
	Color *pix=(Color*)malloc(sizeof(Color));
	pix->R=red;
	pix->G=green;
	pix->B=blue;
	
	return pix;
}

//The main body of Life; given an image and a rule, computes one iteration of the Game of Life.
//You should be able to copy most of this from steganography.c
Image *life(Image *image, uint32_t rule)
{
	//YOUR CODE HERE
	Image* ans=malloc(sizeof(Image));
	ans->cols=image->cols;
	ans->rows=image->rows;

	Color **img=(Color**)malloc(sizeof(Color*)*ans->rows*ans->cols);
	ans->image=img;
	for(int i=0;i<ans->rows;i++)
	{
		for(int j=0;j<ans->cols;j++)
		{
			*(img)=evaluateOneCell(image, i, j,rule);
			img++;
		}
	}
	return ans;
}

/*
Loads a .ppm from a file, computes the next iteration of the game of life, then prints to stdout the new image.

argc stores the number of arguments.
argv stores a list of arguments. Here is the expected input:
argv[0] will store the name of the program (this happens automatically).
argv[1] should contain a filename, containing a .ppm.
argv[2] should contain a hexadecimal number (such as 0x1808). Note that this will be a string.
You may find the function strtol useful for this conversion.
If the input is not correct, a malloc fails, or any other error occurs, you should exit with code -1.
Otherwise, you should return from main with code 0.
Make sure to free all memory before returning!

You may find it useful to copy the code from steganography.c, to start.
*/
int main(int argc, char **argv)
{
	//YOUR CODE HERE
	if(argc<=2)
	{
		printf("请输入.ppm参数\n");
	}
	char *filename=argv[1];
	char *endptr;
	long num=strtol(argv[2], &endptr, 16);
	uint32_t rule=(int)num;
	Image *image=readData(filename);
	Image *message=life(image,rule);
	writeData(message);
	freeImage(image);
	freeImage(message);
}

```
## 思路
```
实现的三个函数和partA2基本一致，最大的变动在于evaluateOneCell（）函数的实现。只需要计数。记录每个bit位附近存活的个数，确定下一个存活状态就可以。具体可以看代码的注释
```