
# 实现.ppm文件的读入，写出，释放
```c
#include <stdio.h>
#include <stdlib.h>
#include <inttypes.h>
#include <string.h>
#include <sys/types.h>
#include "imageloader.h"

//Opens a .ppm P3 image file, and constructs an Image object. 
//You may find the function fscanf useful.
//Make sure that you close the file with fclose before returning.
Image *readData(char *filename) 
{
	
	//YOUR CODE HERE
	FILE *fp=fopen(filename,"r");
	if(fp==NULL)
	{
		printf("文件不存在或者打开失败，请重试");
		return NULL;
	}

	char buf[20];
	fscanf(fp, "%s", buf);
	if(buf[0]!='P'||buf[1]!='3')
	{
		printf("打开的不是.ppm文件");
		return NULL;
	}
	int n,m;
	fscanf(fp, "%d%d",&m,&n);
	int range;
	fscanf(fp, "%d",&range);
	if(range!=255)
	{
		printf(".ppm头格式不是0~255");
		return NULL;
	} 
	Image *img=(Image*)malloc(sizeof(Image));

	img->cols=m;
	img->rows=n;

	Color **image=(Color**)malloc(sizeof(Color*)*n*m);

	for(int i=0;i<n*m;i++)
	{
		Color *pix=(Color*)malloc(sizeof(Color));
		fscanf(fp, "%hhu %hhu %hhu",&pix->R,&pix->G,&pix->B);
		*(image+i)=pix;
	}
	img->image=image;
	fclose(fp);
	return img;
}



//Given an image, prints to stdout (e.g. with printf) a .ppm P3 file with the image's data.
void writeData(Image *image)
{
	printf("P3\n");
	printf("%d %d\n",image->cols,image->rows);
	printf("%d\n",255);

	int m=image->cols,n=image->rows;
    Color **img=image->image;
    for(int i=0;i<n;i++)
	{
		for(int j=0;j<m-1;j++)
        {
			printf("%3hhu %3hhu %3hhu   ",(*img)->R,(*img)->G,(*img)->B);
			img++;
		}
		printf("%3hhu %3hhu %3hhu\n",(*img)->R,(*img)->G,(*img)->B);
		img++;
    }
    return;
	//YOUR CODE HERE
}

//Frees an image
void freeImage(Image *image)
{
    Color **img=image->image;
    for(int i=0;i<image->rows*image->cols;i++)
	{
		free((*(img+i)));
    }
	free(img);
	free(image);
	//YOUR CODE HERE
}
```
## 思路
学会使用fopen(),fscanf(),fclose(),free()函数的用法就可以
主要难点是**\*\*image变量的malloc**，这个二级指针，此处有两种写法：
1. 类似于数组，可以申请n个（n是行数）二级指针，然后再遍历n次，每次申请m个（m是列数）一级指针，并且让二级指针指向当前申请的m个一级指针的首部。其实就是类似于数组的形式。
这里每个二级指针指向m个一级指针的首地址，每个一级指针指向一个Color结构体。

我一开始是这么写的，但是后面在PartA2中，实现steganography（）函数的时候，由于evaluateOnePixel（）函数返回值是一个Color\*类型，此处就无法直接用返回值赋值了。
首先是二级指针只有n个，只指向了一级指针的首地址，因此无法对每个一级指针进行赋值操作。因为在申请一级指针的时候，会申请Color结构体所占用的空间。而evaluateOnePixel（）也申请了这个结构体空间，因此重复了，需要释放一个。也就放弃了这种，有重复申请内存和重复释放问题。
当然，另一种处理方式是申请一片Color\*,也就是`(Color**)malloc(sizeof(Color\*)\*m)`,然后再一个一个赋值；但是这个和第二个做法是一样的。
2. 可以申请n\*m个二级指针，循环遍历n\*m次，每次申请一个一级指针，每个二级指针指向一个一级指针，一级指针指向一个Color结构体；这个简单易懂容易实现。