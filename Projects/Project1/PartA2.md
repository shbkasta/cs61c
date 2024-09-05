# 找出ppm文件中的隐藏信息

```c++

#include <stdio.h>
#include <stdlib.h>
#include <inttypes.h>
#include "imageloader.h"

//Determines what color the cell at the given row/col should be. This should not affect Image, and should allocate space for a new Color.
Color *evaluateOnePixel(Image *image, int row, int col)
{
	//YOUR CODE HERE
	Color *color=(Color *)malloc(sizeof(Color));
	Color **img=image->image;
	Color* pix=*(img+row*image->cols+col);
	if(pix->B&1)
	{
		color->R=255;
		color->G=255;
		color->B=255;
	}
	else
	{
		color->R=0;
		color->G=0;
		color->B=0;
	}
	return color;
}

//Given an image, creates a new image extracting the LSB of the B channel.
Image *steganography(Image *image)
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
			*(img)=evaluateOnePixel(image, i, j);
			img++;
		}
	}
	return ans;
}

/*
Loads a file of ppm P3 format from a file, and prints to stdout (e.g. with printf) a new image, 
where each pixel is black if the LSB of the B channel is 0, 
and white if the LSB of the B channel is 1.

argc stores the number of arguments.
argv stores a list of arguments. Here is the expected input:
argv[0] will store the name of the program (this happens automatically).
argv[1] should contain a filename, containing a file of ppm P3 format (not necessarily with .ppm file extension).
If the input is not correct, a malloc fails, or any other error occurs, you should exit with code -1.
Otherwise, you should return from main with code 0.
Make sure to free all memory before returning!
*/
int main(int argc, char **argv)
{
	//YOUR CODE HERE
	if(argc<=1)
	{
		printf("请输入.ppm参数\n");
	}
	char *filename=argv[1];
	Image *image=readData(filename);
	Image *message=steganography(image);
	writeData(message);
	freeImage(image);
	freeImage(message);
}

```
## 思路
把这部分题目意思读懂就比较容易，会PartA1的内存申请和释放即可。这部分是根据读入的.ppm，生成一个新.ppm。最后可以看一个.ppm文件的结果。
使用convert命令将ppm文件转为png文件进行查看

![SecretMessage](./../pictures/secretMessage.png)
