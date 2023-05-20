# C-language-classset-genshinimpact-easyx
It‘s my project of C Language programming class design. And it's a game of wilderness survival, but I use abundant of genshin recources to build it. Hopeful you can like it.
//原代码
#define _CRT_SECURE_NO_WARNINGS 1
#include <stdio.h>;
#include <string.h>;
#include <conio.h>;
#include <easyx.h>;
#include <math.h>;
#include <graphics.h>;
#include <time.h>;
#include <Windows.h>;
#include <mmSystem.h>;
#pragma comment(lib,"winmm.lib")
#pragma comment( lib, "MSIMG32.LIB")
#define PI 3.14159265359
#define WindowWidth 1920
#define WindowHeight 1080
typedef struct map {
	int x;
	int y;
	int number;
	int mode;
	int ele;
	int lei;
	char name[10];
	IMAGE *img;
	struct map* next;
}MAP;
typedef struct music {
	int x1;
	int x2;
	int y1;
	int y2;
	int number;
	char name[10];
	struct music* next;
}MUSIC;
typedef struct lyric {
	int x1;
	int y1;
	int yl1;
	int yl2;
	int number;
	int number1;
	int number2;
	int mode;
	char name[10];
	IMAGE* img;
	struct lyric* next;
}LYRIC;
typedef struct ele {
	int ranking[3];
	int maxscore;
	int deatht;
	int damt;
	int juqing;
	int battlejq;
	int time;
	int xnow;
	int ynow;
	int poisonnum;
	int enemy;
	int molanumber;
	int fruitnum;
	int furniture[6];
	int tppoint[20];
	int house;
	int monster;
	int ele[7];
	int level=0;
	int att=0;
	int attele = 0;
	int attend=0;
	int elenow1=0;
	int elenow2=0;
	int life=0;
	int cm = 0;
	int finallife = 0;
	int norshield=0;
	int difshield=0;
	int eleshield = 0;
	int immune = 0;
	int grasscore=0;
	int fire=0;
	int freeze=0;
	int active=0;
	wchar_t name[10];
	char* ch;
	MAP* headroom;
	struct ele* next;
}ELE;
MAP* box();
MAP* monster();
MAP* animal();
MAP* tppoint();
MUSIC* musicset();
ELE* enemyset0();
ELE* enemyset1();
ELE* enemyset2();
ELE* enemyset3();
ELE* enemyset4();
ELE* enemyset5();
ELE* enemyset6();
ELE* enemyset7();
ELE* enemyset8();
void juqing();
void battlejuqing();
void mapopen(ELE* character);
void park(ELE* character);
void room(ELE* character);
void beginning(ELE* character);
void ending(ELE* character);
void eating(ELE* character);
void damage(int xnow, int ynow, ELE* character);
void statusmon(ELE* elehead);
void statuscha(ELE* character);
void backpack(ELE* character);
void monsterset(int xnow, int ynow, MAP* headm);
void boxset(int xnow, int ynow, MAP* head);
void mapset(int xnow,int ynow,IMAGE d);
void setpoint(ELE* character, int xnow, int ynow);
LYRIC* lyricset1();
LYRIC* lyricset2();
LYRIC* lyricset3();
LYRIC* lyricset4();
MUSIC* musicplay(int dx,int dy,int xnow, int ynow, MUSIC* musichead, ELE* character);
void animalset(int dx, int dy, int xnow, int ynow, MAP* heada, ELE* character);
void tp(int xnow, int ynow, int dx, int dy, ELE* character, MAP* tphead);
void boxopen(int x1, int y1, int dx, int dy, MAP* h, int xnow, int ynow, int force, ELE* character);
void musicgame(LYRIC* headm1, LYRIC* headm2, LYRIC* headm3, LYRIC* headm4);
void battleon(int x1, int y1, int dx, int dy, MAP* headm, int xnow, int ynow, int force,ELE* character, ELE* elehead);
ELE* elereaction(ELE* q, ELE* character,ELE* k);
void drawalpha(IMAGE* picture, int  picture_x, int picture_y); //x为要载入图片的X坐标，y为Y坐标
void drawalpha(IMAGE* picture, int  picture_x, int picture_y) //x为载入图片的X坐标，y为Y坐标
{

	// 变量初始化
	DWORD* dst = GetImageBuffer();    // GetImageBuffer()函数，用于获取绘图设备的显存指针，EASYX自带
	DWORD* draw = GetImageBuffer();
	DWORD* src = GetImageBuffer(picture); //获取picture的显存指针
	int picture_width = picture->getwidth(); //获取picture的宽度，EASYX自带
	int picture_height = picture->getheight(); //获取picture的高度，EASYX自带
  	int graphWidth = getwidth();       //获取绘图区的宽度，EASYX自带
	int graphHeight = getheight();     //获取绘图区的高度，EASYX自带
	int dstX = 0;    //在显存里像素的角标

	// 实现透明贴图 公式： Cp=αp*FP+(1-αp)*BP ， 贝叶斯定理来进行点颜色的概率计算
	for (int iy = 0; iy < picture_height; iy++)
	{
		for (int ix = 0; ix < picture_width; ix++)
		{
			int srcX = ix + iy * picture_width; //在显存里像素的角标
			int sa = ((src[srcX] & 0xff000000) >> 24); //0xAArrggbb;AA是透明度
			int sr = ((src[srcX] & 0xff0000) >> 16); //获取RGB里的R
			int sg = ((src[srcX] & 0xff00) >> 8);   //G
			int sb = src[srcX] & 0xff;              //B
			if (ix >= 0 && ix <= graphWidth && iy >= 0 && iy <= graphHeight && dstX <= graphWidth * graphHeight)
			{
				dstX = (ix + picture_x) + (iy + picture_y) * graphWidth; //在显存里像素的角标
				int dr = ((dst[dstX] & 0xff0000) >> 16);
				int dg = ((dst[dstX] & 0xff00) >> 8);
				int db = dst[dstX] & 0xff;
				draw[dstX] = ((sr * sa / 255 + dr * (255 - sa) / 255) << 16)  //公式： Cp=αp*FP+(1-αp)*BP  ； αp=sa/255 , FP=sr , BP=dr
					| ((sg * sa / 255 + dg * (255 - sa) / 255) << 8)         //αp=sa/255 , FP=sg , BP=dg
					| (sb * sa / 255 + db * (255 - sa) / 255);              //αp=sa/255 , FP=sb , BP=db
			}
		}
	}
}
void drawalphaAA(IMAGE* picture, int  picture_x, int picture_y,double AA=1) //x为载入图片的X坐标，y为Y坐标
{

	// 变量初始化
	DWORD* dst = GetImageBuffer();    // GetImageBuffer()函数，用于获取绘图设备的显存指针，EASYX自带
	DWORD* draw = GetImageBuffer();
	DWORD* src = GetImageBuffer(picture); //获取picture的显存指针
	int picture_width = picture->getwidth(); //获取picture的宽度，EASYX自带
	int picture_height = picture->getheight(); //获取picture的高度，EASYX自带
	int graphWidth = getwidth();       //获取绘图区的宽度，EASYX自带
	int graphHeight = getheight();     //获取绘图区的高度，EASYX自带
	int dstX = 0;    //在显存里像素的角标

	// 实现透明贴图 公式： Cp=αp*FP+(1-αp)*BP ， 贝叶斯定理来进行点颜色的概率计算
	for (int iy = 0; iy < picture_height; iy++)
	{
		for (int ix = 0; ix < picture_width; ix++)
		{
			int srcX = ix + iy * picture_width; //在显存里像素的角标
			int sa = ((src[srcX] & 0xff000000) >> 24)*AA; //0xAArrggbb;AA是透明度
			int sr = ((src[srcX] & 0xff0000) >> 16); //获取RGB里的R
			int sg = ((src[srcX] & 0xff00) >> 8);   //G
			int sb = src[srcX] & 0xff;              //B
			if (ix >= 0 && ix <= graphWidth && iy >= 0 && iy <= graphHeight && dstX <= graphWidth * graphHeight)
			{
				dstX = (ix + picture_x) + (iy + picture_y) * graphWidth; //在显存里像素的角标
				int dr = ((dst[dstX] & 0xff0000) >> 16);
				int dg = ((dst[dstX] & 0xff00) >> 8);
				int db = dst[dstX] & 0xff;
				draw[dstX] = ((sr * sa / 255 + dr * (255 - sa) / 255) << 16)  //公式： Cp=αp*FP+(1-αp)*BP  ； αp=sa/255 , FP=sr , BP=dr
					| ((sg * sa / 255 + dg * (255 - sa) / 255) << 8)         //αp=sa/255 , FP=sg , BP=dg
					| (sb * sa / 255 + db * (255 - sa) / 255);              //αp=sa/255 , FP=sb , BP=db
			}
		}
	}
}
void drawAlphaplus(IMAGE* image, int x, int y, int width, int height, int pic_x, int pic_y, double AA = 1)
{
	// 变量初始化
	DWORD* dst = GetImageBuffer();			// GetImageBuffer() 函数，用于获取绘图设备的显存指针， EasyX 自带
	DWORD* draw = GetImageBuffer();
	DWORD* src = GetImageBuffer(image);		// 获取 picture 的显存指针
	int imageWidth = image->getwidth();		// 获取图片宽度
	int imageHeight = image->getheight();	// 获取图片宽度
	int dstX = 0;							// 在 绘图区域 显存里像素的角标
	int srcX = 0;							// 在 image 显存里像素的角标

	// 实现透明贴图 公式： Cp=αp*FP+(1-αp)*BP ， 贝叶斯定理来进行点颜色的概率计算
	for (int iy = 0; iy < imageHeight; iy++)
	{
		for (int ix = 0; ix < imageWidth; ix++)
		{
			// 防止越界
			if (ix + pic_x >= 0 && ix + pic_x < imageWidth && iy + pic_y >= 0 && iy + pic_y < imageHeight &&
				ix + x >= 0 && ix + x < WindowWidth && iy + y >= 0 && iy + y < WindowHeight)
			{
				// 获取像素角标
				int srcX = (ix + pic_x) + (iy + pic_y) * imageWidth;
				dstX = (ix + x) + (iy + y) * WindowWidth;

				int sa = ((src[srcX] & 0xff000000) >> 24) * AA;			// 0xAArrggbb; AA 是透明度
				int sr = ((src[srcX] & 0xff0000) >> 16);				// 获取 RGB 里的 R
				int sg = ((src[srcX] & 0xff00) >> 8);					// G
				int sb = src[srcX] & 0xff;								// B

				// 设置对应的绘图区域像素信息
				int dr = ((dst[dstX] & 0xff0000) >> 16);
				int dg = ((dst[dstX] & 0xff00) >> 8);
				int db = dst[dstX] & 0xff;
				draw[dstX] = ((sr * sa / 255 + dr * (255 - sa) / 255) << 16)  //公式： Cp=αp*FP+(1-αp)*BP  ； αp=sa/255 , FP=sr , BP=dr
					| ((sg * sa / 255 + dg * (255 - sa) / 255) << 8)         //αp=sa/255 , FP=sg , BP=dg
					| (sb * sa / 255 + db * (255 - sa) / 255);              //αp=sa/255 , FP=sb , BP=db
			}
		}
	}
}
void drawAlphaplus0(IMAGE* image, int x, int y, int pic_x, int pic_y, double AA = 1)
{
	// 变量初始化
	DWORD* dst = GetImageBuffer();			// GetImageBuffer() 函数，用于获取绘图设备的显存指针， EasyX 自带
	DWORD* draw = GetImageBuffer();
	DWORD* src = GetImageBuffer(image);		// 获取 picture 的显存指针
	int imageWidth = image->getwidth();		// 获取图片宽度
	int imageHeight = image->getheight();	// 获取图片宽度
	int dstX = 0;							// 在 绘图区域 显存里像素的角标
	int srcX = 0;							// 在 image 显存里像素的角标

	// 实现透明贴图 公式： Cp=αp*FP+(1-αp)*BP ， 贝叶斯定理来进行点颜色的概率计算
	for (int iy = 0; iy < imageHeight; iy++)
	{
		for (int ix = 0; ix < imageWidth; ix++)
		{
			// 防止越界
			if (ix + pic_x >= 0 && ix + pic_x < imageWidth && iy + pic_y >= 0 && iy + pic_y < imageHeight &&
				ix + x >= 0 && ix + x < WindowWidth && iy + y >= 0 && iy + y < WindowHeight)
			{
				// 获取像素角标
				int srcX = (ix + pic_x) + (iy + pic_y) * imageWidth;
				dstX = (ix + x) + (iy + y) * WindowWidth;

				int sa = ((src[srcX] & 0xff000000) >> 24) * AA;			// 0xAArrggbb; AA 是透明度
				int sr = ((src[srcX] & 0xff0000) >> 16);				// 获取 RGB 里的 R
				int sg = ((src[srcX] & 0xff00) >> 8);					// G
				int sb = src[srcX] & 0xff;								// B

				// 设置对应的绘图区域像素信息
				int dr = ((dst[dstX] & 0xff0000) >> 16);
				int dg = ((dst[dstX] & 0xff00) >> 8);
				int db = dst[dstX] & 0xff;
				draw[dstX] = ((sr * sa / 255 + dr * (255 - sa) / 255) << 16)  //公式： Cp=αp*FP+(1-αp)*BP  ； αp=sa/255 , FP=sr , BP=dr
					| ((sg * sa / 255 + dg * (255 - sa) / 255) << 8)         //αp=sa/255 , FP=sg , BP=dg
					| (sb * sa / 255 + db * (255 - sa) / 255);              //αp=sa/255 , FP=sb , BP=db
			}
		}
	}
}
int main() {
	int key, molamember = 0, distance = 0, x1 =800, y1 =400, xnow = 7100, ynow = 3000,dx=0,dy=0,damt=0,j=0;
	int left = 0, top = 0, right = 100, bottom = 100,inn=0;
	HRGN Area = CreateRectRgn(left, top, right, bottom);
	double a = 1;
	MAP* head = NULL, * headm = NULL,*heada=NULL,*tphead=NULL;
	MUSIC* musichead;
	ELE* elehead=NULL, * character;
	character = (ELE*)malloc(sizeof(ELE));
	character->maxscore=0;
	character->molanumber = 0;
	character->fruitnum = 3;
	for (inn = 0; inn < 7; inn++) {
		character->ele[inn] = 0;
	}
	for (inn = 0; inn < 3; inn++) {
		character->ranking[inn] = 0;
	}
	for (inn = 0; inn < 6; inn++) {
		character->furniture[inn] = 0;
	}
	for (inn = 0; inn < 20; inn++) {
		character->tppoint[inn] = 0;
	}
	character->house = 0;
	character->damt = 0;
	character->time = 0;
	character->deatht = 0;
	character->juqing = 0;
	character->battlejq = 0;
	character->xnow = 7100;//7100
	character->ynow = 3100;//3100
	character->poisonnum = 4;
	character->life = 40;
	character->cm = 0;
	character->finallife = 40;
	character->level = 10;
	character->difshield = 0;
	character->norshield = 0;
	character->eleshield = 0;
	character->att = 0;
	character->attend = 0;
	character->elenow1 = 0;
	character->elenow2 = 0;
	character->grasscore = 0;
	character->fire = 0;
	character->freeze = 0;
	character->active = 0;
	character->headroom = NULL;
	character->next = NULL;
	IMAGE d, back,process;
	initgraph(1920, 1080, SHOWCONSOLE); // 初始化绘图环境，EASYX自带，现定义绘图区分辨率700x700
	setbkcolor(WHITE);               //用于设置当前绘图背景色，EASYX自带
	setbkmode(TRANSPARENT);
	settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
	cleardevice();                  //是用当前背景色清空屏幕，并将当前点移至 (0, 0)，EASYX自带
	loadimage(&d, L"map.png", 16003, 6672);
	loadimage(&process, L"转场.png", 1920, 1080);
	//set区域
	beginning(character);
	for (inn = 0; inn < 55; inn++) {
		BeginBatchDraw();
		drawAlphaplus0(&process, 0, 0, 0, 0, inn / 65.0);
		FlushBatchDraw();
		Sleep(17);
	}
	xnow = character->xnow;
	ynow = character->ynow;
	head = box();
	headm = monster();
	musichead = musicset();
	setaspectratio(2, 2);
	tphead = tppoint();
	heada=animal();
	for (inn = 20; inn > 0; inn--) {
		BeginBatchDraw();
		cleardevice();
		putimage(0, 0, 1920, 1080, &d, xnow-440, ynow-270);
		drawAlphaplus0(&process, 0, 0, 0, 0, inn / 21.0);
		FlushBatchDraw();
		Sleep(10);
	}
	mapset(3000, 6000, d);
	setaspectratio(1, 1);
	if (character->juqing == 0) {
		juqing();
		character->juqing = 1;
	}
	//图片加载代码区
	IMAGE imgW1L, imgW1R, imgW1, imgH1L, imgH1R, imgH1;
	loadimage(&imgW1R, L"./LRIGHT.png", 196, 294);
	loadimage(&imgW1L, L"./LLEFT.png", 202, 294);
	loadimage(&imgW1, L"./LNORMAL.png", 196, 296);
	loadimage(&imgH1R, L"./RRIGHT.png", 202, 294);
	loadimage(&imgH1L, L"./RLEFT.png", 192, 300);
	loadimage(&imgH1, L"./RNORMAL.png", 198, 296);
	int t = 0, value, xmid = 960, dertaD, lp, dertaY, ymid = 540, force = 0,dertax=50,dertay=50;
	ExMessage msg;
	while (1) {
		//方向控制代码
		lp = _kbhit();
		if (lp != 0) {
			dertax = 50;
			dertay = 40;
			if (GetAsyncKeyState(VK_CONTROL)) {
				dertax = dertax + 20;
				dertay = dertay + 16;
			}
			if (GetAsyncKeyState(VK_RIGHT)) {
				dertaD = xmid - x1;
				printf("%d", dertaD);
				if (dertaD > -350) {
					x1 += dertax;
					dx += dertax;
				}
				else {
					xnow += dertax/2;
				}
				force = 0;
			}
			if (GetAsyncKeyState(VK_LEFT)) {
				dertaD = xmid - x1;
				printf("%d", dertaD);
				if (dertaD < 350) {
					x1 -= dertax;
					dx -= dertax;
				}
				else {
					xnow -= dertax/2;
				}
				force = 1;
			}
			if (GetAsyncKeyState(VK_DOWN)) {
				dertaY = ymid - y1;
				printf("%d", dertaY);
				if (dertaY > -160) {
					y1 += dertay;
					dy += dertay;
				}
				else {
					ynow += dertay/2;
				}
			}
			if (GetAsyncKeyState(VK_UP)) {
				dertaY = ymid - y1;
				printf("%d", dertaY);
				if (dertaY < 200) {
					y1 -= dertay;
					dy -= dertay;
				}
				else {
					ynow -= dertay/2;
				}
			}
		}
		backpack(character);
		musichead = musicplay(dx, dy, xnow, ynow, musichead,character);
		flushmessage(EX_KEY);
		//时间等待代码
		Sleep(100);
		t++;
	    //持续作图
		cleardevice();
		BeginBatchDraw();
		setaspectratio(2, 2);
		mapset(xnow, ynow, d);
		setaspectratio(1, 1);
		boxset(xnow, ynow,head);
		monsterset(xnow, ynow,headm);
		//右向角色动图
		if (force == 0) {
			if (t % 4 == 0 && t % 2 == 0) {
				drawalpha(&imgH1L, x1-28, y1 - 23);
			}
			else if (t % 2 == 1) {
				drawalpha(&imgH1, x1-28, y1-20);
			}
			else if (t % 4 == 2 && t % 2 == 0) {
				drawalpha(&imgH1R, x1-28, y1-20);
			}
		}
		//左向角色动图
		if (force == 1) {
			if (t % 4 == 0 && t % 2 == 0) {
				drawalpha(&imgW1L, x1-10, y1-23);
			}
			else if (t % 2 == 1) {
				drawalpha(&imgW1, x1-10, y1-23);
			}
			else if (t % 4 == 2 && t % 2 == 0) {
				drawalpha(&imgW1R, x1-10, y1-23);
			}
		}
		//区块检测互动代码
		boxopen(x1,y1,dx, dy, head, xnow, ynow,force, character);
		animalset(dx,dy,xnow, ynow,heada, character);
		character->xnow = xnow;
		character->ynow = ynow;
		tp(xnow, ynow, dx, dy, character, tphead);
		if(xnow != character->xnow){
			xnow = character->xnow;
			ynow = character->ynow;
		}
		switch (character->level) {
		case(10):
			elehead = enemyset0();
			break;
		case(20):
			elehead = enemyset1();
			break;
		case(30):
			elehead = enemyset2();
			break;
		case(40):
			elehead = enemyset3();
			break;
		case(50):
			elehead = enemyset4();
			break;
		case(60):
			elehead = enemyset5();
			break;
		case(70):
			elehead = enemyset6();
			break;
		case(80):
			elehead = enemyset7();
			break;
		case(90):
			elehead = enemyset8();
			break;
		}
		battleon(x1,y1,dx, dy, headm, xnow, ynow, force, character, elehead);
		damage(xnow, ynow, character);
		statuscha(character);
		eating(character);
		settextcolor(WHITE);
		wchar_t filename[100];
		swprintf_s(filename, L"按下v查看地图");
		outtextxy(1600, 50, filename);
		swprintf_s(filename, L"按下up,down,right,left以移动");
		outtextxy(1490, 100, filename);
		swprintf_s(filename, L"按下ctrl以加速");
		outtextxy(1600, 150, filename);
		swprintf_s(filename, L"按下F与物品交互");
		outtextxy(1600, 200, filename);
		if (character->life < 40) {
			swprintf_s(filename, L"按下T使用生命药水,按下E食用日落果");
			outtextxy(1400, 250, filename);
		}
		swprintf_s(filename, L"UID:");
		wcscat(filename, character->name);
		outtextxy(1500, 1000,filename);
		FlushBatchDraw();
		lp = _kbhit();
		int ttt=0;
		if (lp != 0) {
			if (GetAsyncKeyState(27)) {
				flushmessage(EX_KEY);
				while (1) {
					BeginBatchDraw();
					swprintf_s(filename, L"按ESC退出游戏");
					outtextxy(800, 350, filename);
					FlushBatchDraw();
					char end = 0;
					end = _getch();
					if (end == 27) {
						character->xnow = xnow;
						character->ynow = ynow;
						ending(character);
						ttt = 1;
						break;
					}
				}
				if (ttt == 1) {
					break;
				}
			}
			if (GetAsyncKeyState(77)) {
				LYRIC* headm1=NULL,*headm2=NULL,*headm3=NULL,*headm4=NULL;
						headm1 = lyricset1();
						headm2 = lyricset2();
						headm3 = lyricset3();
						headm4 = lyricset4();
				musicgame(headm1, headm2, headm3, headm4);
				MUSIC* q;
				q = musichead;
				while (q != NULL) {
					if (q->number == 1) {
						q->number = 0;
					}
					q = q->next;
				}
			}
			if (GetAsyncKeyState(86)) {
				mapopen(character);
				if (xnow != character->xnow) {
					xnow = character->xnow;
					ynow = character->ynow;
				}
			}
			if (GetAsyncKeyState(80)) {
				if (character->house == 1) {
					BeginBatchDraw();
					settextstyle(100, 50, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
					swprintf_s(filename, L"Loading...");
					outtextxy(500, 500, filename);
					FlushBatchDraw();
					settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
					park(character);
				}
			}
		}
	}
}

void mapset(int xnow,int ynow,IMAGE d) {
	int i = -1,j=0,mem=0,xmap,ymap;
	xmap = xnow-440;
	ymap = ynow-270;
	putimage(0, 0, 1920, 1080, &d, xmap, ymap);
}

void mapopen(ELE* character) {
	int i = 0,i0=0, tp = 0;
	IMAGE maph, d[10];
	wchar_t filename[100];
	loadimage(&maph, L"map.png", 1920, 950);
	loadimage(&d[0], L"传送.png", 37, 50);
	loadimage(&d[1], L"烟花背景0.png", 1920, 1080);
	double x[20] = { 7400,5600,5100,7100,5550,3630,4950,3630,2090,2110,2100,2100,60,130,10370,11621,13001,14200,15101,15101 }, y[20] = { 100,1550,120,2481,2471,3000,4751,4771,5990,5170,4450,3850,5290,4160,4600,4420,5250,4050,3770,5250 };
	while (1) {
		BeginBatchDraw();
		cleardevice();
		drawAlphaplus0(&d[1], 0, 0, 0, 0, 1);
		drawAlphaplus0(&maph, 0, 0, 0, 0, 1);
		swprintf_s(filename, L"荒岛冒险地图（含传送锚点）");
		outtextxy(100,100,filename);
		swprintf_s(filename, L"按下ESC回到主界面");
		outtextxy(100, 200, filename);
		swprintf_s(filename, L"按下传送点对应的按键便可传送，未解锁区域的传送点不可传送");
		outtextxy(100, 1000, filename);
		i0 = 0;
		for (i = 0; i < 23; i++) {
			if (i == 15) { i+=1; }
			if (i == 1) { i+=1; }
			if (i == 12) { i+=1; }
			swprintf_s(filename, L"%c", (char)(i+65));
			drawAlphaplus0(&d[0], 1920 * (x[i0]+160) / 16003.0, 950 * (y[i0]+150) / 6672.0, 0, 0, 1);
			outtextxy(1920 * (x[i0] + 160) / 16003.0, 950 * (y[i0] + 150) / 6672, filename);
			i0++;
		}
		FlushBatchDraw();
		i = 0;
		i0 = 0;
		if (GetAsyncKeyState(27)) {
			break;
		}
		while (i < 23) {
			if (i == 15) { i++; }
			if (i == 1) { i++; }
			if (i == 12) { i++; }
			if (GetAsyncKeyState(65 + i)) {
				if (character->tppoint[i0] == 1) {
					tp = 1;
					character->xnow = (int)x[i0];
					character->ynow = (int)y[i0];
					printf("%d", character->xnow);
					printf("%d", character->ynow);
				}
				else {
					BeginBatchDraw();
					swprintf_s(filename, L"该传送点未解锁");
					outtextxy(1400, 100, filename);
					FlushBatchDraw();
					Sleep(1000);
				}
				break;
			}
			i++;
			i0++;
		}
		if (tp == 1) {
			break;
		}
	}
	flushmessage();
}

void boxset(int xnow, int ynow,MAP* head) {
	int i = 0, j = 0, mem = 0, xmap, ymap;
	IMAGE d[10];
	wchar_t filename[100];
	swprintf_s(filename, L"宝箱.png");
	loadimage(&d[0], filename, 52, 51);
	loadimage(&d[2], L"宝箱空.png", 54, 51);
	MAP map[20];
	MAP* p, * h, * q = NULL;
	q = head;
	while (q != NULL) {
		q->img = &d[0];
		i++;
		if (q->x<xnow+400 && q->x>xnow-400 && q->y<ynow+170 && q->y>ynow-220 && q->number==0) {//打印范围内的物品+960
			drawAlphaplus0(q->img,(q->x-xnow)*2+960, (q->y-ynow)*2+540,0,0,1);
		}
		if (q->x<xnow+400 && q->x>xnow - 400 && q->y<ynow + 220 && q->y>ynow - 220 && q->number == 1) {//打印范围内的物品
			q->img = &d[2];
			drawAlphaplus0(q->img, (q->x - xnow)*2 + 960, (q->y - ynow)*2 + 540,0,0,1);
		}
		q = q->next;
	}
}

void boxopen(int x1, int y1, int dx, int dy, MAP* h, int xnow, int ynow, int force,ELE* character) {
	IMAGE d[10];
	loadimage(&d[1], L"宝箱开.png", 55, 70);
	loadimage(&d[2], L"宝箱空.png", 54, 51);
	loadimage(&d[3], L"./02.png", 73, 34);
	int i,ran;
	MAP* q;
	q = h;
	while (q != NULL) {
		if (q->x < xnow+30+dx/2 && q->x > xnow-100+dx/2 && q->y<ynow+80+dy/2  && q->y>ynow-100+dy/2 && q->number == 0) {
			wchar_t filename[100];
			swprintf_s(filename, L"play 宝箱.mp3");
			mciSendString(filename, NULL, 0, NULL);
			q->img = &d[1];
			drawalpha(q->img, (q->x-xnow)*2+960, (q->y-ynow)*2+540);
			ran = rand()%10+1;
			swprintf_s(filename, L"获得%d摩拉",ran*100);
			character->molanumber += ran * 100;
			outtextxy(1700, 200, filename);
			if (force == 0) {
				drawalpha(&d[3], x1 + 55, y1 + 80);
			}
			else drawalpha(&d[3], x1 + 33, y1 + 70);
			FlushBatchDraw();
			Sleep(1500);
			q->img = &d[2];
			q->number = 1;
			BeginBatchDraw();
			break;
		}
		q = q->next;
	}
}

MAP* box() {
	int i = -1, j = 0, mem = 0, xmap, ymap;
	IMAGE d[10];
	MAP map[20];
	wchar_t filename[100];
	swprintf_s(filename, L"宝箱.png");
	loadimage(&d[0], filename, 52, 51);
	MAP* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 288; j++) {
		if (j % 18 == 0) {
			i++;
		}
		p = (MAP*)malloc(sizeof(MAP));
		p->img = &d[0];
		mem = j % 28;
		p->x = 0 + (1000 * mem);
		p->y = 0 + 600 * i;
		p->number = 0;
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}

MAP* monster() {
	int i, j = 0,x[15]={900,550,3090,3220,4200,4300,5400,5800,6200,9500,8000,10800,12600,14300,14900},y[15]={4828,6200,4850,6200,5600,4100,5250,560,2200,2100,3200,5800,4950,6100,4200};
	MAP* head = NULL, * p, * h, * q = NULL;//输入物品
	char ch[10]="蕈兽";
	for (i = 0; i < 15; i++) {
		p = (MAP*)malloc(sizeof(MAP));
		p->x = x[i];
		p->y = y[i];
		//strcpy(p->name, ch);
		p->number = 0;
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}

void monsterset(int xnow, int ynow, MAP* headm) {
	int i = 0, j = 0, mem = 0, xmap, ymap;
	IMAGE d[11];
	loadimage(&d[0], L"蕈兽.png",220,176 );
	loadimage(&d[1], L"丘丘萨满.png", 200, 212);
	loadimage(&d[2], L"丘丘人.png", 160, 200);
	loadimage(&d[3], L"火深渊法师.png", 224, 196);
	loadimage(&d[4], L"风史莱姆.png", 204, 120);
	loadimage(&d[5], L"qqr.png", 190, 186);
	loadimage(&d[6], L"蕈兽2.png", 147, 212);
	loadimage(&d[7], L"蕈兽3.png", 177, 148);
	loadimage(&d[8], L"丘丘壁挂.png", 200, 200);
	loadimage(&d[9], L"爆炎树.png", 200, 200);
	loadimage(&d[10], L"炉灶之魔神.png", 200, 200);
	MAP* q = NULL;
	q = headm;
	while (q != NULL) {
		if (i <= 10) {
			q->img = &d[i];
		}
		else q->img = &d[i - 11];
		i++;
		if (q->x<xnow + 360 && q->x>xnow - 360 && q->y<ynow + 170 && q->y>ynow - 220) {//打印范围内的物品
			drawAlphaplus0(q->img, (q->x - xnow) * 2 + 960, (q->y - ynow) * 2 + 540,0,0,1);
		}
		q = q->next;
	}
}
void statuscha(ELE* character) {
	IMAGE d[10];
	if (character->life >= 20) {
		setfillcolor(GREEN);
	}
	else if (character->life >= 10) {
		setfillcolor(RGB(222,203,0));
	}
	else {
		setfillcolor(RED);
	}
	wchar_t filename[100];
	swprintf_s(filename, L"Lv.%d",character->level);
	outtextxy(400, 970, filename);
	POINT pts[] = { {500, 985}, {510, 970}, {1300, 970},{1310,985},{1300,1000},{510,1000} };
	fillpolygon(pts, 6);
	swprintf_s(filename, L"%d", character->life*1000);
	outtextxy(855, 970, filename);
	loadimage(&d[0], L"背包.png", 74, 78);
	drawalpha(&d[0],1400,945);
	if (character->time > 0) {
		loadimage(&d[1], L"寒冷.png", 1920, 1080);
		drawAlphaplus(&d[1], 0, 0, 1920, 1080, 0, 0, character->time / 130.0);
	}
	if (character->deatht > 0) {
		loadimage(&d[2], L"碎屏.png", 1920, 1080);
		drawAlphaplus(&d[2], 0, 0, 1920, 1080, 0, 0, character->deatht / 100.0);
	}
	swprintf_s(filename, L"B:");
	outtextxy(1370, 970, filename);
	swprintf_s(filename, L"M: 小游戏(音游）");
	outtextxy(1480, 970, filename);
	if (character->time > 0) {
		setfillcolor(LIGHTBLUE);
		POINT pts[] = { {500, 935}, {510, 920}, {510+8*character->time, 920},{520 + 8 * character->time,935},{510 + 8 * character->time,950},{510,950} };
		fillpolygon(pts, 6);
		if (character->time > 81) {
			swprintf_s(filename, L"00:0%d", 50 - character->time / 2);
		}
		else swprintf_s(filename, L"00:%d", 50-character->time/2);
		outtextxy(1350, 920, filename);
	}
	if (character->deatht > 0) {
		setfillcolor(BLACK);
		POINT pts[] = { {500, 935}, {510, 920}, {510 + 8 * character->deatht, 920},{520 + 8 * character->deatht,935},{510 + 8 * character->deatht,950},{510,950} };
		fillpolygon(pts, 6);
		swprintf_s(filename, L"00:0%d", 5 - character->deatht / 20);
		outtextxy(1350, 920, filename);
	}
}
void statusmon(ELE* elehead) {
	int i=0,a=330;
	ELE* q;
	q = elehead;
	wchar_t filename[100];
	while (q != NULL) {
		if ((double)q->life/(double)q->finallife>0.5) {
			setfillcolor(GREEN);
		}
		else if ((double)q->life/(double)q->finallife >= 0.3) {
			setfillcolor(RGB(222, 203, 0));
		}
		else {
			setfillcolor(RED);
		}
		swprintf_s(filename, L"Lv.%d", q->level);
		outtextxy(400+a*i, 70, filename);
		POINT pts[] = { {500+a*i, 85}, {510+a*i, 70}, {700+a*i, 70},{710+a*i,85},{700 + a * i,100},{510 + a * i,100} };
		fillpolygon(pts, 6);
		swprintf_s(filename, L"%d", q->life * 1000);
		outtextxy(560+a*i, 70, filename);
		i++;
		q = q->next;
	}
}
void backpack(ELE* character) {
	IMAGE d[10];
	ExMessage msg;
	if (GetAsyncKeyState(66)) {
		wchar_t filename[100];
		loadimage(&d[0], L"backbag.png", 1920, 1080);
		loadimage(&d[1], L"果.png", 85, 75);
		loadimage(&d[2], L"生命药水.png", 85, 85);
		loadimage(&d[3], L"生命药水.png", 183, 183);
		while (1) {
			cleardevice();
			BeginBatchDraw();
			drawalpha(&d[0], 0, 0);
			drawalpha(&d[1], 75, 120);
			drawalpha(&d[2], 75, 210);
			swprintf_s(filename, L"%d", character->molanumber);
			outtextxy(160, 70, filename);
			swprintf_s(filename, L"%d", character->fruitnum);
			outtextxy(160, 145, filename);
			swprintf_s(filename, L"%d", character->poisonnum);
			outtextxy(160, 240, filename);
			POINT pts[] = { {1850, 50}, {1860, 50}, {1890, 90},{1880,90} };
			fillpolygon(pts, 4);
			POINT pts2[] = { {1890, 50}, {1880, 50}, {1850, 90},{1860,90} };
			fillpolygon(pts2, 4);
			circle(1870, 70, 25);
			swprintf_s(filename, L"按下S键以进入商城");
			outtextxy(1600, 300, filename);
			swprintf_s(filename, L"按下ESC键以退出");
			outtextxy(1600, 350, filename);
			if (GetAsyncKeyState(83)) {
				while (1) {
					cleardevice();
					drawalpha(&d[0], 0, 0);
					drawalpha(&d[3], 800, 400);
					swprintf_s(filename, L"500摩拉一瓶(恢复10000点生命)");
					outtextxy(800, 600, filename);
					swprintf_s(filename, L"按下F键以购买");
					outtextxy(1600, 400, filename);
					swprintf_s(filename, L"家园家具1号：按下1键以购买");
					outtextxy(1500, 450, filename);
					if (character->furniture[0] == 1) {
						swprintf_s(filename, L"已拥有");
						outtextxy(1400, 450, filename);
					}
					swprintf_s(filename, L"家园家具2号：按下2键以购买");
					outtextxy(1500, 500, filename);
					if (character->furniture[1] == 1) {
						swprintf_s(filename, L"已拥有");
						outtextxy(1400, 500, filename);
					}
					swprintf_s(filename, L"家园家具3号：按下3键以购买");
					outtextxy(1500, 550, filename);
					if (character->furniture[2] == 1) {
						swprintf_s(filename, L"已拥有");
						outtextxy(1400, 550, filename);
					}
					swprintf_s(filename, L"家园家具4号：按下4键以购买");
					outtextxy(1500,600, filename);
					if (character->furniture[3] == 1) {
						swprintf_s(filename, L"已拥有");
						outtextxy(1400, 600, filename);
					}
					swprintf_s(filename, L"家园家具5号：按下5键以购买");
					outtextxy(1500, 650, filename);
					if (character->furniture[4] == 1) {
						swprintf_s(filename, L"已拥有");
						outtextxy(1400, 650, filename);
					}
					swprintf_s(filename, L"家园家具6号：按下6键以购买");
					outtextxy(1500, 700, filename);
					if (character->furniture[5] == 1) {
						swprintf_s(filename, L"已拥有");
						outtextxy(1400, 700, filename);
					}
					swprintf_s(filename, L"按下ESC键以退出");
					outtextxy(1600, 350, filename);
					if (GetAsyncKeyState(70)) {
						if (character->molanumber >= 500) {
							character->molanumber -= 500;
							character->poisonnum += 1;
							swprintf_s(filename, L"购买成功");
							outtextxy(800, 700, filename);
						}
						else {
							swprintf_s(filename, L"摩拉不足");
							outtextxy(800, 700, filename);
						}
					}
					if (GetAsyncKeyState(49)) {
						if (character->molanumber >= 5000) {
							character->molanumber -= 5000;
							character->furniture[0] = 1;
							swprintf_s(filename, L"购买成功1");
							outtextxy(800, 700, filename);
						}
						else {
							swprintf_s(filename, L"摩拉不足");
							outtextxy(800, 700, filename);
						}
					}
					if (GetAsyncKeyState(50)) {
						if (character->molanumber >= 5000) {
							character->molanumber -= 5000;
							character->furniture[1] = 1;
							swprintf_s(filename, L"购买成功2");
							outtextxy(800, 700, filename);
						}
						else {
							swprintf_s(filename, L"摩拉不足");
							outtextxy(800, 700, filename);
						}
					}
					if (GetAsyncKeyState(51)) {
						if (character->molanumber >= 5000) {
							character->molanumber -= 5000;
							character->furniture[2] = 1;
							swprintf_s(filename, L"购买成功3");
							outtextxy(800, 700, filename);
						}
						else {
							swprintf_s(filename, L"摩拉不足");
							outtextxy(800, 700, filename);
						}
					}
					if (GetAsyncKeyState(52)) {
						if (character->molanumber >= 5000) {
							character->molanumber -= 5000;
							character->furniture[3] = 1;
							swprintf_s(filename, L"购买成功4");
							outtextxy(800, 700, filename);
						}
						else {
							swprintf_s(filename, L"摩拉不足");
							outtextxy(800, 700, filename);
						}
					}
					if (GetAsyncKeyState(53)) {
						if (character->molanumber >= 5000) {
							character->molanumber -= 5000;
							character->furniture[4] = 1;
							swprintf_s(filename, L"购买成功5");
							outtextxy(800, 700, filename);
						}
						else {
							swprintf_s(filename, L"摩拉不足");
							outtextxy(800, 700, filename);
						}
					}
					if (GetAsyncKeyState(54)) {
						if (character->molanumber >= 5000) {
							character->molanumber -= 5000;
							character->furniture[5] = 1;
							swprintf_s(filename, L"购买成功6");
							outtextxy(800, 700, filename);
						}
						else {
							swprintf_s(filename, L"摩拉不足");
							outtextxy(800, 700, filename);
						}
					}
					FlushBatchDraw();
					Sleep(1000);
					if (GetAsyncKeyState(27)) {
						break;
					}
				}
			}
			FlushBatchDraw();
			if (GetAsyncKeyState(27)) {
				break;
			}
		}
	}
}
MUSIC* musicset() {
	int i, j = 0, x1[20] = {7280,5500,5000,7000,5450,3530,4850,3530,2000,2000,2000,2000,0,0,9970,11521,12901,14000,15001,15001}, x2[20] = {10100,10300,7279,9000,6999,5459,7350,4849,3529,3529,3529,3529,1999,1999,11520,12900,15000,15000,16003,16003}, y1[20] = {0,1501,0,2431,2431,2950,4701,4701,5940,5120,4200,2900,5240,4110,4250,4250,5200,3700,3700,5200}, y2[20] = {1500,2430,1500,3950,4700,4700,6672,6672,6672,5939,5119,4199,6672,5239,6672,6672,6672,5199,5199,6672};
	MUSIC* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 20; j++) {
		p = (MUSIC*)malloc(sizeof(MUSIC));
		p->x1 = x1[j];
		p->x2 = x2[j];
		p->y1 = y1[j];
		p->y2 = y2[j];
		p->number = 0;
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
MUSIC* musicplay(int dx, int dy, int xnow,int ynow,MUSIC* musichead,ELE* character) {
	int i=0;
	MUSIC* q;
	q = musichead;
	while (q != NULL) {
		if (q->number == 1) {
			if (q->x1 > xnow + dx/2 || q->x2 < xnow + dx/2 || q->y1>ynow + dy/2 || q->y2<ynow + dy/2 && q->number == 0) {
				q->number = 0;
			}
		}
		q = q->next;
	}
	q = musichead;
	while (q != NULL) {
		if (q->x1 < xnow + dx/2 && q->x2 > xnow + dx/2 && q->y1<ynow + dy/2 && q->y2>ynow + dy/2 && q->number == 0) {
			q->number = 1;
			PlaySound(NULL,NULL, SND_PURGE);
			wchar_t filename[100];
			swprintf_s(filename, L"%d.wav", i+1);
			PlaySound(filename, NULL, SND_ASYNC | SND_FILENAME | SND_LOOP);
			if (character->tppoint[i] == 0) {
				character->tppoint[i] = 1;
			}
			break;
		}
		q = q->next;
		i++;
	}
	return musichead;
}
ELE* elereaction(ELE* elehead,ELE* character,ELE* k) {//1:水元素2：火元素3：冰元素4：雷元素5：风元素6：岩元素7：草元素
	int a,i,attele,att,value=0,j=0,number=0;
	ELE* q;
	wchar_t filename[100];
	q = elehead;
	int status = 0;//感电，超载，超导
	int status2 = 0;//扩散
	attele = character->attele;
	att = character->att;
	switch (attele) {
	case(1):
		if (character->furniture[0] == 1) {
			character->life += 1;
		}
		break;
	case(2):
		if (character->furniture[4] == 1) {
			att+= 1;
		}
		break;
	case(3):
		if (character->furniture[3] == 1) {
			att+= 1;
		}
		break;
	case(4):
		if (character->furniture[1] == 1) {
			att+= 1;
		}
		break;
	case(5):
		if (character->furniture[1] == 1) {
			att+= 1;
		}
		break;
	case(6):
		if (character->furniture[5] == 1) {
			att+= 1;
		}
		break;
	case(7):
		if (character->furniture[2] == 1) {
			att+= 1;
		}
		break;
	}
	if (q->elenow2 == 7) {
		a = q->elenow1;
		q->elenow1 = q->elenow2;
		q->elenow2 = a;
	}
	if (q->elenow2 == 0) {
		if (q->elenow1 == 0) {
			switch (attele) {
			case(0):
				value = 1;
				break;
			case(1):
				q->elenow1 = 1;
				value = 1;
				break;
			case(2):
				if (character->grasscore != 0) {
					att += 2;
					character->grasscore -= 1;
				}
				q->elenow1 = 2;
				value = 1;
				break;
			case(3):
				q->elenow1 = 3;
				value = 1;
				break;
			case(4):
				if (character->active != 0) {
					att += 1;
					character->active -= 1;
				}
				if (character->grasscore != 0) {
					att += 2;
					character->grasscore -= 1;
				}
				q->elenow1 = 4;
				value = 1;
				break;
			case(5):
				value = 1;
				break;
			case(6):
				value = 1;
				break;
			case(7):
				if (character->active != 0) {
					att += 1;
					character->active -= 1;
				}
				q->elenow1 = 7;
				value = 1;
				break;
			}
		}
		if (value == 0) {
			if (q->elenow1 == 1) {
				switch (attele) {
				case(0):
					break;
				case(1):
					break;
				case(2):
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
					}
					att = att + 2;
					q->elenow1 = 0;
					settextcolor(RED);
					outtextxy(1200, 300, L"蒸发");
					break;
				case(3):
					att = att + 2;
					q->freeze = 1;
					q->elenow1 = 0;
					settextcolor(LIGHTBLUE);
					outtextxy(1200, 300, L"冻结");
					break;
				case(4):
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
					}
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
					}
					att += 1;//感电
					status += 1;
					q->elenow1 = 0;
					settextcolor(MAGENTA);
					outtextxy(1200, 300, L"感电");
					break;
				case(5):
					att += 1;//扩散
					status2 = attele;
					q->elenow1 = 0;
					settextcolor(LIGHTGREEN);
					outtextxy(1200, 300, L"扩散");
					break;
				case(6):
					att += 1;
					character->norshield += 1;
					q->elenow1 = 0;
					settextcolor(YELLOW);
					outtextxy(1200, 300, L"结晶");
					break;
				case(7):
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
					}
					att += 1;
					character->grasscore += 1;
					q->elenow1 = 0;
					settextcolor(GREEN);
					outtextxy(1200, 300, L"绽放");
					break;
				}
			}
			if (q->elenow1 == 2) {
				switch (attele) {
				case(0):
					break;
				case(1):
					att += 2;
					q->elenow1 = 0;
					settextcolor(BLUE);
					outtextxy(1200, 300, L"蒸发");
					break;
				case(2):
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
						settextcolor(RED);
						outtextxy(1200, 300, L"烈绽放");
					}
					break;
				case(3):
					att += 2;
					q->elenow1 = 0;
					settextcolor(LIGHTBLUE);
					outtextxy(1200, 300, L"融化");
					break;
				case(4):
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
						settextcolor(MAGENTA);
						outtextxy(1200, 400, L"超激化");
					}
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
						settextcolor(MAGENTA);
						outtextxy(1200, 500, L"超绽放");
					}
					att += 1;//超载
					status += 1;
					q->elenow1 = 0;
					settextcolor(MAGENTA);
					outtextxy(1200, 300, L"超载");
					if (elehead->next != NULL) {
						elehead = elehead->next;
					}
					else elehead = k;
					break;
				case(5):
					att += 1;//扩散
					status2 = attele;
					q->elenow1 = 0;
					settextcolor(LIGHTGREEN);
					outtextxy(1200, 300, L"扩散");
					break;
				case(6):
					att += 1;
					character->norshield += 1;
					q->elenow1 = 0;
					settextcolor(YELLOW);
					outtextxy(1200, 300, L"结晶");
					break;
				case(7):
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
						settextcolor(GREEN);
						outtextxy(1200, 400, L"蔓激化");
					}
					att += 1;
					character->fire += 1;//燃烧
					q->elenow1 = 0;
					settextcolor(GREEN);
					outtextxy(1200, 300, L"燃烧");
					break;
				}
			}
			if (q->elenow1 == 3) {
				switch (attele) {
				case(0):
					break;
				case(1):
					att += 1;
					q->freeze = 1;//冻结
					q->elenow1 = 0;
					settextcolor(LIGHTBLUE);
					outtextxy(1200, 300, L"冻结");
					break;
				case(2):
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
						settextcolor(RED);
						outtextxy(1200, 400, L"烈绽放");
					}
					att += 2;
					q->elenow1 = 0;
					settextcolor(RED);
					outtextxy(1200, 300, L"融化");
					break;
				case(3):
					break;
				case(4):
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
						settextcolor(MAGENTA);
						outtextxy(1200, 400, L"超激化");
					}
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
						settextcolor(MAGENTA);
						outtextxy(1200, 500, L"超绽放");
					}
					att += 1;
					status += 1;//超导
					q->elenow1 = 0;
					settextcolor(MAGENTA);
					outtextxy(1200, 300, L"超导");
					break;
				case(5):
					att += 1;
					status2 = attele;//扩散
					q->elenow1 = 0;
					settextcolor(LIGHTBLUE);
					outtextxy(1200, 300, L"扩散");
					break;
				case(6):
					att += 1;
					character->norshield += 1;
					q->elenow1 = 0;
					settextcolor(YELLOW);
					outtextxy(1200, 300, L"结晶");
					break;
				case(7):
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
						settextcolor(GREEN);
						outtextxy(1200, 300, L"蔓激化");
					}//不反应
					q->elenow2 = 7;
					break;
				}
			}
			if (q->elenow1 == 4) {
				switch (attele) {
				case(0):
					break;
				case(1):
					att += 1;
					status += 1;//感电
					q->elenow1 = 0;
					settextcolor(BLUE);
					outtextxy(1200, 300, L"感电");
					break;
				case(2):
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
						settextcolor(RED);
						outtextxy(1200, 400, L"烈绽放");
					}
					status += 1;//超载
					q->elenow1 = 0;
					settextcolor(RED);
					outtextxy(1200, 300, L"超载");
					if (elehead->next != NULL) {
						elehead = elehead->next;
					}
					else elehead = k;
					break;
				case(3):
					att += 1;
					status += 1;//超导
					q->elenow1 = 0;
					settextcolor(LIGHTBLUE);
					outtextxy(1200, 300, L"超导");
					break;
				case(4):
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
						settextcolor(MAGENTA);
						outtextxy(1200, 400, L"超绽放");
					}
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
						settextcolor(MAGENTA);
						outtextxy(1200, 300, L"超激化");
					}
					break;
				case(5):
					att += 1;
					status2 = attele;//扩散
					q->elenow1 = 0;
					settextcolor(LIGHTGREEN);
					outtextxy(1200, 300, L"扩散");
					break;
				case(6):
					att += 1;
					character->norshield += 1;
					q->elenow1 = 0;
					settextcolor(YELLOW);
					outtextxy(1200, 300, L"结晶");
					break;
				case(7):
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
						settextcolor(GREEN);
						outtextxy(1200, 400, L"蔓激化");
					}
					att += 1;
					character->active = 3;//激化
					q->elenow1 = 0;
					settextcolor(GREEN);
					outtextxy(1200, 300, L"原激化");
					break;
				}
			}
			if (q->elenow1 == 7) {
				switch (attele) {
				case(0):
					break;
				case(1):
					att += 1;
					character->grasscore += 1;//绽放
					q->elenow1 = 0;
					settextcolor(BLUE);
					outtextxy(1200, 300, L"绽放");
					break;
				case(2):
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
						settextcolor(RED);
						outtextxy(1200, 400, L"烈绽放");
					}
					att += 1;
					character->fire += 1;//燃烧
					settextcolor(RED);
					outtextxy(1200, 300, L"燃烧");
					break;
				case(3):
					q->elenow2 = 3;//不反应
					break;
				case(4):
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
						settextcolor(MAGENTA);
						outtextxy(1200, 500, L"超激化");
					}
					if (character->grasscore != 0) {
						att += 2;
						character->grasscore -= 1;
						settextcolor(MAGENTA);
						outtextxy(1200, 400, L"超绽放");
					}
					att += 1;
					character->active = 3;//激化
					settextcolor(MAGENTA);
					outtextxy(1200, 300, L"原激化");
					break;
				case(5):
					break;
				case(6):
					q->elenow2 = 6;//不反应
					break;
				case(7):
					if (character->active != 0) {
						att += 1;
						character->active -= 1;
						settextcolor(GREEN);
						outtextxy(1200, 300, L"蔓激化");
					}
					q->life -= att;
					break;
				}
			}
		}
	}
	else if (q->elenow2 == 3) {
		switch (attele) {
		case(0):
			break;
		case(1):
			att += 1;
			q->freeze = 1;
			q->elenow2 = 0;
			settextcolor(LIGHTBLUE);
			outtextxy(1200, 300, L"冻结");
			break;
		case(2):
			if (character->grasscore != 0) {
				att += 2;
				character->grasscore -= 1;
				settextcolor(RED);
				outtextxy(1200, 400, L"烈绽放");
			}
			att += 2;
			q->elenow2 = 0;
			settextcolor(RED);
			outtextxy(1200, 300, L"融化");
			break;
		case(3):
			break;
		case(4):
			if (character->active != 0) {
				att += 1;
				character->active -= 1;
				settextcolor(MAGENTA);
				outtextxy(1200, 300, L"超激化");
			}
			if (character->grasscore != 0) {
				att += 2;
				character->grasscore -= 1;
				settextcolor(MAGENTA);
				outtextxy(1200, 300, L"超绽放");
			}
			att += 1;
			status += 1;//超导
			q->elenow2 = 0;
			settextcolor(MAGENTA);
			outtextxy(1200, 300, L"超导");
			break;
		case(5):
			att += 1;
			status2 = attele;
			q->elenow2 = 0;
			settextcolor(LIGHTGREEN);
			outtextxy(1200, 300, L"扩散");
			break;
		case(6):
			att += 1;
			character->norshield += 1;
			q->elenow2 = 0;
			settextcolor(YELLOW);
			outtextxy(1200, 300, L"结晶");
			break;
		case(7):
			break;
		}
		character->att = 0;
		character->elenow1 = 0;
	}
	if (q->immune == attele) {
		att= 0;
	}
	if (q->eleshield != 0) {
		if (attele == 2) {
			att = 0;
		}
		else {
			q->eleshield -= 1;
			att = 0;
			if (q->eleshield == 0) {
				q->freeze = 1;
			}
		}
	}
	if (q->difshield != 0 && att>0) {
		q->difshield -= 1;
		att -= 1;
	}
	if (q->norshield != 0 && att > 0) {
		if (q->norshield >= att) {
			q->norshield -= att;
			att = 0;
		}
		else att -= q->norshield;
	}
	if (att > 0) {
		q->life -= att;
	}
	settextcolor(WHITE);
	swprintf_s(filename, L"-%d", att * 1000);
	if (character->cm == 0) {
		outtextxy(1700, 300, filename);
	}
	else {
		settextcolor(YELLOW);
		outtextxy(1600, 700, filename);
		settextcolor(WHITE);
	}
	printf(" %d", q->freeze);
	if (q->next != NULL) {
		q = q->next;
	}
	else number = 1;
	while (q != NULL && number==0) {
		att = 0;
		if (status != 0) {
			for (i = 0; i < status; i++) {
				att++;
				q->life -= 1;
			}
		}
		if (status2 != 0) {
			attele = status2;
			att += 1;
			if (q->elenow2 == 7) {
				a = q->elenow1;
				q->elenow1 = q->elenow2;
				q->elenow2 = a;
			}
			if (q->elenow2 == 0) {
				if (q->elenow1 == 0) {
					switch (attele) {
					case(1):
						q->life -= att;
						q->elenow1 = 1;
						break;
					case(2):
						q->life -= att;
						q->elenow1 = 2;
						break;
					case(3):
						q->life -= att;
						q->elenow1 = 3;
						break;
					case(4):
						q->life -= att;
						q->elenow1 = 4;
						break;
					case(5):
						q->life -= att;
						break;
					case(6):
						q->life -= att;
						break;
					case(7):
						q->life -= att;
						q->elenow1 = 7;
						break;
					}
				}
				if (q->elenow1 == 1) {
					switch (attele) {
					case(1):
						q->life -= att;
						break;
					case(2):
						att += 2;
						q->life -= att;
						q->elenow1 = 0;
						break;
					case(3):
						att += 1;
						q->life -= att;
						q->freeze = 1;
						q->elenow1 = 0;
						break;
					case(4):
						att += 1;
						q->life -= att;//感电
						status += 1;
						q->elenow1 = 0;
						break;
					case(5):
						att += 1;
						q->life -= att;//扩散
						status2 = attele;
						q->elenow1 = 0;
						break;
					case(6):
						att += 1;
						q->life -= att;
						character->norshield += 1;
						q->elenow1 = 0;
						break;
					case(7):
						att += 1;
						q->life -= att;
						character->grasscore += 1;
						q->elenow1 = 0;
						break;
					}
				}
				if (q->elenow1 == 2) {
					switch (attele) {
					case(1):
						att += 2;
						q->life -= att;
						q->elenow1 = 0;
						break;
					case(2):
						q->life -= att;
						break;
					case(3):
						att += 2;
						q->life -= att;
						q->elenow1 = 0;
						break;
					case(4):
						att += 1;
						q->life -= att;//超载
						status += 1;
						q->elenow1 = 0;
						break;
					case(5):
						att += 1;
						q->life -= att;//扩散
						status2 = attele;
						q->elenow1 = 0;
						break;
					case(6):
						att += 1;
						q->life -= att;
						character->norshield += 1;
						q->elenow1 = 0;
						break;
					case(7):
						att += 1;
						q->life -= att;
						character->fire += 1;//燃烧
						q->elenow1 = 0;
						break;
					}
				}
				if (q->elenow1 == 3) {
					switch (attele) {
					case(1):
						att += 1;
						q->life -= att;
						q->freeze = 1;//冻结
						q->elenow1 = 0;
						break;
					case(2):
						att += 2;
						q->life -= att;
						q->elenow1 = 0;
						break;
					case(3):
						q->life -= att;
						break;
					case(4):
						att += 1;
						q->life -= att;
						status += 1;//超导
						q->elenow1 = 0;
						break;
					case(5):
						att += 1;
						q->life -= att;
						status2 = attele;//扩散
						q->elenow1 = 0;
						break;
					case(6):
						att += 1;
						q->life -= att;
						character->norshield += 1;
						q->elenow1 = 0;
						break;
					case(7):
						q->life -= att;//不反应
						q->elenow2 = 7;
						break;
					}
				}
				if (q->elenow1 == 4) {
					switch (attele) {
					case(1):
						att += 1;
						q->life -= att;
						status += 1;//感电
						q->elenow1 = 0;
						break;
					case(2):
						q->life -= att;
						status += 1;//超载
						q->elenow1 = 0;
						break;
					case(3):
						att += 1;
						q->life -= att;
						status += 1;//超导
						q->elenow1 = 0;
						break;
					case(4):
						q->life -= att;
						break;
					case(5):
						att += 1;
						q->life -= att;
						status2 = attele;//扩散
						q->elenow1 = 0;
						break;
					case(6):
						att += 1;
						q->life -= att;
						character->norshield += 1;
						q->elenow1 = 0;
						break;
					case(7):
						att += 1;
						q->life -= att;
						character->active = 3;//激化
						q->elenow1 = 0;
						break;
					}
				}
				if (q->elenow1 == 7) {
					switch (attele) {
					case(1):
						att += 1;
						q->life -= att;
						character->grasscore += 1;//绽放
						q->elenow1 = 0;
						break;
					case(2):
						att += 1;
						q->life -= att;
						character->fire += 1;//燃烧
						break;
					case(3):
						q->life -= att;
						q->elenow2 = 3;//不反应
						break;
					case(4):
						att += 1;
						q->life -= att;
						character->active = 3;//激化
						break;
					case(5):
						q->life -= att;
						break;
					case(6):
						q->life -= att;//不反应
						break;
					case(7):
						q->life -= att;
						break;
					}
				}
			}
			else if (q->elenow2 == 3) {
				switch (attele) {
				case(1):
					att += 1;
					q->life -= att;
					q->freeze = 1;
					q->elenow2 = 0;
					break;
				case(2):
					att += 2;
					q->life -= att;
					q->elenow2 = 0;
					break;
				case(3):
					q->life -= att;
					break;
				case(4):
					att += 1;
					q->life -= att;
					status += 1;//超导
					q->elenow2 = 0;
					break;
				case(5):
					att += 1;
					q->life -= att;
					status2 = attele;
					q->elenow2 = 0;
					break;
				case(6):
					att += 1;
					q->life -= att;
					character->norshield += 1;
					q->elenow2 = 0;
					break;
				case(7):
					q->life -= att;
					break;
				}
			}
		}
		settextcolor(WHITE);
		swprintf_s(filename, L"-%d", att * 1000);
		if (character->cm == 0) {
			outtextxy(1700, 400 + 100 * j, filename);
		}
		else {
			settextcolor(YELLOW);
			outtextxy(1600, 700 + 100 * j, filename);
			settextcolor(WHITE);
		}
		printf("%d", q->freeze);
		q = q->next;
		j++;
	}
	return elehead;
}
void battleon(int x1, int y1, int dx, int dy, MAP* headm, int xnow, int ynow, int force, ELE* character, ELE* elehead) {
	wchar_t filename[100];
	ELE* p, * f,*l,*k;//f为遍历用指针
	MAP* q;
	q = headm;
	l = elehead;
	p = elehead;
	k = elehead;
	f = elehead;
	while (q != NULL) {
		if (q->x < xnow +30 + dx/2 && q->x > xnow -100 + dx/2 && q->y<ynow +80 + dy/2 && q->y>ynow -100 + dy/2 && q->number == 0) {
			settextcolor(WHITE);
			flushmessage(EX_KEY);
			swprintf_s(filename, L"按下F键进入战斗");
			outtextxy(1650, 300, filename);
			FlushBatchDraw();
			if (GetAsyncKeyState(70)) {
				swprintf_s(filename, L"play 战斗.mp3");
				mciSendString(filename, NULL, 0, NULL);
				IMAGE d[120],monster[10],process;
				loadimage(&d[1], L"前景.png", 1920, 417);
				loadimage(&d[2], L"背景.png", 1920, 1080);
				loadimage(&d[3], L"./02.png", 73, 34);
				loadimage(&monster[0], L"蕈兽.png", 420, 326);
				loadimage(&monster[1], L"蕈兽2.png", 294, 420);
				loadimage(&monster[2], L"蕈兽3.png", 354, 296);
				loadimage(&monster[5], L"丘丘萨满？.png", 256, 420);
				loadimage(&monster[6], L"丘丘人.png", 250, 380);
				loadimage(&monster[3], L"火深渊法师.png", 448, 392);
				loadimage(&monster[4], L"风史莱姆.png", 306, 180);
				loadimage(&monster[7], L"炉灶之魔神.png", 600, 600);
				loadimage(&monster[8], L"爆炎树.png", 550, 600);
				loadimage(&d[7], L"battle1.png", 574, 597);
				loadimage(&d[8], L"battle2.png", 574, 597);
				loadimage(&d[9], L"battle3.png", 574, 597);
				loadimage(&d[10], L"battle4.png", 574, 597);
				loadimage(&d[11], L"battle5.png", 574, 597);
				loadimage(&d[16], L"草原核.png", 80, 104);
				loadimage(&d[17], L"草原核爆炸.png", 141, 128);
				loadimage(&d[18], L"ice1.png", 617, 568);
				loadimage(&d[19], L"ice2.png", 617, 568);
				loadimage(&d[20], L"ice3.png", 617, 568);
				loadimage(&d[21], L"ice4.png", 617, 568);
				loadimage(&d[22], L"ice5.png", 617, 568);
				loadimage(&d[23], L"ice6.png", 617, 568);
				loadimage(&d[24], L"ice7.png", 617, 568);
				loadimage(&d[25], L"ice8.png", 617, 568);
				loadimage(&d[26], L"ice9.png", 617, 568);
				loadimage(&d[27], L"ice10.png", 617, 568);
				loadimage(&d[28], L"ice11.png", 617, 568);
				loadimage(&d[29], L"ice12.png", 617, 568);
				loadimage(&d[30], L"ice13.png", 617, 568);
				loadimage(&d[31], L"ice14.png", 617, 568);
				loadimage(&d[33], L"lig-1.png", 852, 708);
				loadimage(&d[34], L"lig0.png", 852, 708);
				loadimage(&d[35], L"lig1.png", 1116, 708);
				loadimage(&d[36], L"lig2.png", 1116, 708);
				loadimage(&d[37], L"lig3.png", 1116, 708);
				loadimage(&d[38], L"lig4.png", 1116, 708);
				loadimage(&d[39], L"lig5.png", 1116, 708);
				loadimage(&d[40], L"lig6.png", 1116, 708);
				loadimage(&d[41], L"lig7.png", 1116, 708);
				loadimage(&d[42], L"lig8.png", 1116, 708);
				loadimage(&d[43], L"lig9.png", 1116, 708);
				loadimage(&d[44], L"attack1.png", 138, 30);
				loadimage(&d[45], L"attack2.png", 135, 196);
				loadimage(&d[46], L"attackying.png", 340, 504);
				loadimage(&d[104], L"皇冠.png",40, 40);
				loadimage(&d[106], L"water8.png", 80, 80);
				loadimage(&d[107], L"fire8.png", 80, 80);
				loadimage(&d[108], L"ice8.png", 80, 80);
				loadimage(&d[109], L"lig0.png", 80, 80);
				loadimage(&d[110], L"wind11.png", 80, 80);
				loadimage(&d[111], L"yan11.png", 80, 80);
				loadimage(&d[112], L"cao12.png", 80, 80);
				loadimage(&d[105], L"attackying.png", 80, 80);
				loadimage(&d[113], L"火护盾.png", 420, 380);
				int i = 0, lp, att, attele, round = 0, check = 0, j1 = 0, j2 = -2, j3 = 0, j4 = 0, j5 = 0, j6 = 0, j7 = 0,im=0, times = 0, time1 = 0,timek=0,j=0,number=0,inn=20;
				for (i = 0; i < 12; i++) {
					swprintf_s(filename, L"wind%d.png", i + 1);
					loadimage(&d[i + 47], filename, 449, 337);
				}
				for (i = 0; i < 10; i++) {
					swprintf_s(filename, L"fire%d.png", i + 1);
					loadimage(&d[i + 59], filename, 1068, 916);
				}
				for (i = 0; i < 11; i++) {
					swprintf_s(filename, L"yan%d.png", i + 1);
					loadimage(&d[i + 69], filename, 1120, 800);
				}
				for (i = 0; i < 12; i++) {
					swprintf_s(filename, L"cao%d.png", i + 1);
					loadimage(&d[i + 80], filename, 482, 330);
				}
				for (i = 0; i < 12; i++) {
					swprintf_s(filename, L"water%d.png", i + 1);
					loadimage(&d[i + 92], filename, 514, 396);
				}
				if (force == 0) {
					drawalpha(&d[3], x1 + 55, y1 + 80);
				}
				else drawalpha(&d[3], x1 + 35, y1 + 70);
				if (character->furniture[0]==1) {
					character->life += 10;
				}
				Sleep(1500);
				loadimage(&process, L"转场.png", 1920, 1080);
				for (i = 0; i < 55; i++) {
					BeginBatchDraw();
					drawAlphaplus0(&process, 0, 0, 0, 0, i / 65.0);
					FlushBatchDraw();
					Sleep(17);
				}
				i = 0;
				char key;
				if (character->battlejq == 0) {
					BeginBatchDraw();
					cleardevice();
					drawalpha(&d[2], 0, 0);
					drawalphaAA(&monster[3], 150, 300, 0.5 + 0.5 * ((double)f->life / (double)f->finallife));
					drawalphaAA(&monster[3], 600, 300, 0.5 + 0.5 * ((double)f->life / (double)f->finallife));
					drawalphaAA(&monster[3], 350, 650, 0.5 + 0.5 * ((double)f->life / (double)f->finallife));
					drawalpha(&d[113], 150, 300);
					swprintf_s(filename, L"剩余盾量：%d", 1);
					outtextxy(450, 300, filename);
					swprintf_s(filename, L"按下数字键以进行本次攻击");
					outtextxy(1550, 100, filename);
					swprintf_s(filename, L"初始行动点数为8");
					outtextxy(1550, 150, filename);
					swprintf_s(filename, L"元素攻击消耗3点，物理2点");
					outtextxy(1550, 200, filename);
					drawalpha(&d[7], 1300, 300);
					drawalpha(&d[1], 0, 663);
					settextcolor(WHITE);
					statuscha(character);
					statusmon(elehead);
					drawalpha(&d[104], 430 + l->enemy * 330, 100);
					battlejuqing();
					character->battlejq = 1;
				}
				while (1) {
					if (l == NULL) {
						l = elehead;
					}
					Sleep(25);
					p = elehead;
					f = elehead;
					k = elehead;
					times++;
					if (times % 9 == 1) {
						i++;
					}
					BeginBatchDraw();
					cleardevice();
					drawalpha(&d[2], 0, 0);
					number = 0;//怪物数量
					if (f->life > 0) {
						drawalphaAA(&monster[f->monster], 150, 300,0.5+0.5*((double)f->life/(double)f->finallife));
						if (f->eleshield > 0) {
							drawalpha(&d[113], 150, 300);
							swprintf_s(filename, L"剩余火元素盾量：%d",f->eleshield);
							outtextxy(450, 300, filename);
						}
						if (f->norshield > 0) {
							drawalpha(&d[113], 150, 300);
							swprintf_s(filename, L"剩余普通盾量：%d", f->norshield);
							outtextxy(450, 250, filename);
						}
						if (f->difshield > 0) {
							drawalpha(&d[113], 150, 300);
							swprintf_s(filename, L"剩余减伤盾量：%d", f->difshield);
							outtextxy(450, 250, filename);
						}
					}
					if (f->next != NULL) {
						f = f->next;
					}
					else number = 1;
					if (f->life > 0 && number==0) {
						drawalphaAA(&monster[f->monster], 600, 300,0.5 + 0.5 * ((double)f->life / (double)f->finallife));
						if (f->eleshield > 0) {
							drawalpha(&d[113], 600, 300);
							swprintf_s(filename, L"剩余火元素盾量：%d", f->eleshield);
							outtextxy(900, 300, filename);
						}
						if (f->norshield > 0) {
							drawalpha(&d[113], 600, 300);
							swprintf_s(filename, L"剩余普通盾量：%d", f->norshield);
							outtextxy(900, 250, filename);
						}
						if (f->difshield > 0) {
							drawalpha(&d[113], 600, 300);
							swprintf_s(filename, L"剩余减伤盾量：%d", f->difshield);
							outtextxy(900, 250, filename);
						}
					}
					if (f->next != NULL) {
						f = f->next;
					}
					else number = 1;
					if (f->life > 0 && number==0) {
						drawalphaAA(&monster[f->monster], 350, 650, 0.5 + 0.5 * ((double)f->life / (double)f->finallife));
						if (f->eleshield > 0) {
							drawalpha(&d[113], 350, 650);
							swprintf_s(filename, L"剩余火元素盾量：%d", f->eleshield);
							outtextxy(650, 650, filename);
						}
						if (f->norshield > 0) {
							drawalpha(&d[113], 350, 650);
							swprintf_s(filename, L"剩余普通盾量：%d", f->norshield);
							outtextxy(650, 600, filename);
						}
						if (f->difshield > 0) {
							drawalpha(&d[113], 350, 650);
							swprintf_s(filename, L"剩余减伤盾量：%d", f->difshield);
							outtextxy(650, 600, filename);
						}
					}
					if (i >= 10) i = i - 8;
					if (i == 1 || i == 9 && i % 6 != 5) {
						drawalpha(&d[7], 1300, 300);
					}
					if (i == 2 || i == 8 && i % 6 == 2) {
						drawalpha(&d[8], 1300, 300);
					}
					if (i == 3 || i == 7) {
						drawalpha(&d[9], 1300, 300);
					}
					if (i == 4 || i == 6) {
						drawalpha(&d[10], 1300, 300);
					}
					if (i == 5) {
						drawalpha(&d[11], 1300, 300);
					}
					if (character->norshield > 0) {
						swprintf_s(filename, L"剩余普通盾：%d", character->norshield);
						outtextxy(1250, 300, filename);
					}
					if (character->difshield > 0) {
						swprintf_s(filename, L"剩余次数盾：%d", character->difshield);
						outtextxy(1250, 350, filename);
					}
					drawalpha(&d[1], 0, 663);
					settextcolor(WHITE);
					statuscha(character);
					statusmon(elehead);
					drawalpha(&d[104], 430+l->enemy*330, 100);
					if (inn > 0) {
						drawAlphaplus0(&process, 0, 0, 0, 0, inn / 21.0);
						inn-=2;
					}
					lp = _kbhit();
					if (lp != 0) {
						if (GetAsyncKeyState(72)) {
							battlejuqing();
						}
						if (GetAsyncKeyState(84)) {
							int line1 = 0,line2=0;
							f = elehead;
							wchar_t filename[100];
							BeginBatchDraw();
							cleardevice();
							drawalpha(&d[2], 0, 0);
							drawalphaAA(&monster[f->monster], 150, 300, 0.5 + 0.5 * ((double)f->life / (double)f->finallife));
							f = f->next;
							if (f != NULL) {
								drawalphaAA(&monster[f->monster], 600, 300, 0.5 + 0.5 * ((double)f->life / (double)f->finallife));
								f = f->next;
							}
							if (f != NULL) {
								drawalphaAA(&monster[f->monster], 350, 650, 0.5 + 0.5 * ((double)f->life / (double)f->finallife));
							}
							f = elehead;
							drawalpha(&d[113], 150, 300);
							swprintf_s(filename, L"剩余盾量：%d", f->eleshield);
							outtextxy(450, 300, filename);
							drawalpha(&d[1], 0, 663);
							settextcolor(YELLOW);
							settextstyle(40, 20, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
							swprintf_s(filename, L"按下TAB键以退出");
							outtextxy(0, 50, filename);
							settextcolor(WHITE);
							settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
							while (f != NULL) {
								swprintf_s(filename, L"%d-怪物信息：等级%d", line2+1,f->level);
								outtextxy(1000, 100 + line2 * 340, filename);
								swprintf_s(filename, L"血量：%d", f->life);
								outtextxy(1000, 140 + line2 * 340, filename);
								switch (f->attele) {
								case(0):
									swprintf_s(filename, L"攻击：对角色造成%d点物理伤害", f->att);
									outtextxy(1000, 180 + line2 * 340, filename);
									if (f->immune != -1) {
										line1++;
										swprintf_s(filename, L"特性：物理伤害免疫");
										outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
									}
									break;
								case(1):
									settextcolor(BLUE);
									swprintf_s(filename, L"攻击：对角色造成%d点水元素伤害", f->att);
									outtextxy(1000, 180 + line2 * 340, filename);
									if (f->immune != -1) {
										line1++;
										swprintf_s(filename, L"特性：水元素伤害免疫");
										outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
									}
									break;
								case(2):
									settextcolor(RED);
									swprintf_s(filename, L"攻击：对角色造成%d点火元素伤害", f->att);
									outtextxy(1000, 180 + line2 * 340, filename);
									if (f->immune != -1) {
										line1++;
										swprintf_s(filename, L"特性：火元素伤害免疫");
										outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
									}
									break;
								case(3):
									settextcolor(LIGHTBLUE);
									swprintf_s(filename, L"攻击：对角色造成%d点冰元素伤害", f->att);
									outtextxy(1000, 180 + line2 * 340, filename);
									if (f->immune != -1) {
										line1++;
										swprintf_s(filename, L"特性：冰元素伤害免疫");
										outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
									}
									break;
								case(4):
									settextcolor(MAGENTA);
									swprintf_s(filename, L"攻击：对角色造成%d点雷元素伤害", f->att);
									outtextxy(1000, 180 + line2 * 340, filename);
									if (f->immune != -1) {
										line1++;
										swprintf_s(filename, L"特性：雷元素伤害免疫");
										outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
									}
									break;
								case(5):
									settextcolor(LIGHTGREEN);
									swprintf_s(filename, L"攻击：对角色造成%d点风元素伤害", f->att);
									outtextxy(1000, 180 + line2 * 340, filename);
									if (f->immune != -1) {
										line1++;
										swprintf_s(filename, L"特性：风元素伤害免疫");
										outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
									}
									break;
								case(6):
									settextcolor(YELLOW);
									swprintf_s(filename, L"攻击：对角色造成%d点岩元素伤害", f->att);
									outtextxy(1000, 180 + line2 * 340, filename);
									if (f->immune != -1) {
										line1++;
										swprintf_s(filename, L"特性：岩元素伤害免疫");
										outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
									}
									break;
								case(7):
									settextcolor(GREEN);
									swprintf_s(filename, L"攻击：对角色造成%d点草元素伤害", f->att);
									outtextxy(1000, 180 + line2 * 340, filename);
									if (f->immune != -1) {
										line1++;
										swprintf_s(filename, L"特性：草元素伤害免疫");
										outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
									}
									break;
								}
								settextcolor(WHITE);
								if (f->fire > 0) {
									line1++;
									swprintf_s(filename, L"效果：回合结束对角色造成1点火元素伤害，剩余%d次", f->fire);
									outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
								}
								if (f->freeze > 0) {
									line1++;
									swprintf_s(filename, L"效果：冻结（本回合无法行动）");
									outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
								}
								if (f->active > 0) {
									line1++;
									swprintf_s(filename, L"效果：激化领域，雷草元素攻击伤害+1，剩余%d次", f->active);
									outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
								}
								if (f->grasscore > 0) {
									line1++;
									swprintf_s(filename, L"效果：异绽放，雷火元素攻击伤害+1，剩余%d次", f->grasscore);
									outtextxy(1000, 180 + line1 * 40 + line2 * 340, filename);
								}
								line1 = 0;
								f = f->next;
								line2++;
							}
							FlushBatchDraw();
							while (1) {
								if (GetAsyncKeyState(9)) {
									break;
								}
							}
						}
						if (GetAsyncKeyState(48)) {
							character->att = 3;
							character->attele = 0;
							round += 2;
							check = 1;
							drawalpha(&d[46], 950, 300);
						}
						if (character->ele[0] == 1) {
							if (GetAsyncKeyState(49)) {
								character->att = 1;
								character->life += 2;
								character->attele = 1;
								if (character->life > 50) {
									character->life = 50;
								}
								round += 3;
								check = 1;
								j7 = 12;
							}
						}
						if (character->ele[1] == 1) {
							if (GetAsyncKeyState(50)) {
								character->att = 2;
								character->attele = 2;
								round += 3;
								check = 1;
								j4 = 10;
							}
						}
						if (character->ele[2] == 1) {
							if (GetAsyncKeyState(51)) {
								character->att = 1;
								character->attele = 3;
								character->difshield += 3;
								round += 3;
								check = 1;
								j1 = 14;
							}
						}
						if (character->ele[3] == 1) {
							if (GetAsyncKeyState(52)) {
								character->att = 1;
								character->attele = 4;
								character->attend += 2;
								round += 3;
								check = 1;
								j2 = 9;
							}
						}
						if (character->ele[4] == 1) {
							if (GetAsyncKeyState(53)) {
								character->att = 2;
								character->attele = 5;
								round += 3;
								check = 1;
								j3 = 12;
							}
						}
						if (character->ele[5] == 1) {
							if (GetAsyncKeyState(54)) {
								character->att = 2;
								character->attele = 6;
								round += 3;
								check = 1;
								j5 = 11;
							}
						}
						if (character->ele[6] == 1) {
							if (GetAsyncKeyState(55)) {
								character->att = 2;
								character->attele = 7;
								round += 3;
								check = 1;
								j6 = 12;
							}
						}
						flushmessage(EX_KEY);
						if (check == 1) {
							l=elereaction(l, character,k);
							p = elehead;
							f = elehead;
							printf(" %d", l->life);
							if (p->life > 0) {
								if (p->freeze == 1) {

								}
								else {
									drawalpha(&d[45], 800, 500);
									time1 = 1;
									k = character;
									elereaction(character, p, k);
									if (p->fire > 2 && p->fire <= 10) {
										p->fire = 2;
									}
									if (p->active > 2) {
										p->active = 2;
									}
								}
							}
							number = 0;
							if (p->next != NULL) {
								p = p->next;
							}
							else number = 1;
							if (p->life > 0 &&number==0) {
								if (p->freeze == 1) {

								}
								else {
									drawalpha(&d[44], 400, 300);
									time1 = 1;
									k = character;
									elereaction(character, p, k);
									if (p->fire >= 2 && p->fire <= 10) {
										p->fire = 2;
									}
									if (p->active > 2) {
										p->active = 2;
									}
								}
							}
							if (p->next != NULL) {
								p = p->next;
							}
							else number = 1;
							if (p->life > 0 && number==0) {
								if (p->freeze == 1) {

								}
								else {
									drawalpha(&d[44], 650, 700);
									time1 = 1;
									k = character;
									elereaction(character, p, k);
									if (p->fire >= 2 && p->fire <= 10) {
										p->fire = 2;
									}
									if (p->active > 2) {
										p->active = 2;
									}
								}
							}
							if (character->life <= 0) {
								settextcolor(YELLOW);
								settextstyle(40, 20, _T("Consolas"), 0, 0, 100, FALSE, FALSE, FALSE);
								loadimage(&d[14], L"青铜.png", 1920, 1080);
								drawalpha(&d[14], 0, 0);
								swprintf_s(filename, L"%d", character->life*1000);
								outtextxy(910, 700, filename);
								for (j = 0; j < 3; j++) {
									swprintf_s(filename, L"%d", character->ranking[j]);
									outtextxy(910, 480 + 50 * j, filename);
								}
								FlushBatchDraw();
								Sleep(5000);
								settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
								break;
							}
							if ((round - 8) >= -1) {
								swprintf_s(filename, L"回合结束,进行额外攻击");
								outtextxy(1550, 150, filename);
								swprintf_s(filename, L"己方护盾清零,行动点重置");
								outtextxy(1550, 200, filename);
								if (character->fire != 0) {
									character->att = 1;
									character->elenow1 = 2;
									k = elehead;
									l=elereaction(l, character,k);
									p = elehead;
									f = elehead;
									character->fire -= 1;
								}
								if (character->attend != 0) {
									character->att = 1;
									character->elenow1 = 4;
									k = elehead;
									l=elereaction(l, character,k);
									character->attend -= 1;
								}
								round = 0;
								character->difshield = 0;
								character->norshield = 0;
								while (f!=NULL) {
									f->freeze = 0;
									f = f->next;
								}
								FlushBatchDraw();
								Sleep(1000);
								BeginBatchDraw();
							}
							else {
								swprintf_s(filename, L"剩余行动点数%d",8-round);
								outtextxy(1550, 150, filename);
							}
							check = 0;
						}
					}
					k = elehead;
					if (l->life <= 0) {
						if (l->next == NULL) {
							k = elehead;
							if (l == k) {
								timek = 3;
							}
							else {
								if (k->life <= 0) {
									if (k->next == l) {
										timek = 3;
									}
									else {
										k = k->next;
										if (k->life <= 0) {
											timek = 3;
										}
										else {
											l = k;
										}
									}
								}
								else {
									l = k;
								}
							}
						}
						else {
							k = l->next;
							if (k->life <= 0) {
								if (k->next == NULL) {
									k = elehead;
									if (k == l) {
										timek = 3;
									}
									else {
										if (k->life <= 0) {
											timek = 3;
										}
										else {
											l = k;
										}
									}
								}
								else {
									k = k->next;
									if (k->life <= 0) {
										timek = 3;
									}
									else {
										l = k;
									}
								}
							}
							else {
								l = k;
							}
						}
						if (timek==3) {
							if (character->life > 10 && character->level<90) {
								character->level += 10;
								swprintf_s(filename, L"角色等级已提升至%d级",character->level);
								outtextxy(1000, 450, filename);
								swprintf_s(filename, L"按下鼠标左键以确认");
								outtextxy(1000, 500, filename);
								FlushBatchDraw();
								while (1) {
									if (MouseHit()) {
										MOUSEMSG msg = GetMouseMsg();
										if (msg.uMsg == WM_LBUTTONDOWN) {
											break;
										}
									}
								}
								BeginBatchDraw();
							}
							else if (character->life > 10 && character->level == 90) {
								IMAGE yanhua[20],back[10];
								int ran0[5], ran1[5], ran=0,ran2=0,ran3=0,ran20=0,ran21=0,ran30=0,ran31=0, t = 0, i = 0,j=0, j1 = 0, j2 = 0, j3 = 0, j4 = 0, j5 = 0, j6 = 0, value1 = 0, xmid = 960, dertaD, lp, dertaY, ymid = 540, force = 0, dertax = 50, dertay = 50, xnow = 960, ynow = 540, x1 = 800, y1 = 700, dx = 0, dy = 0;
								IMAGE imgW1L, imgW1R, imgW1, imgH1L, imgH1R, imgH1;
								loadimage(&imgW1R, L"./LRIGHT.png", 196, 294);
								loadimage(&imgW1L, L"./LLEFT.png", 202, 294);
								loadimage(&imgW1, L"./LNORMAL.png", 196, 296);
								loadimage(&imgH1R, L"./RRIGHT.png", 202, 294);
								loadimage(&imgH1L, L"./RLEFT.png", 192, 300);
								loadimage(&imgH1, L"./RNORMAL.png", 198, 296);
								loadimage(&yanhua[0], L"烟花6.png", 256, 256);
								loadimage(&yanhua[1], L"烟花8.png", 359, 338);
								loadimage(&yanhua[2], L"烟花4.png", 323, 305);
								loadimage(&yanhua[3], L"烟花7.png", 436, 411);
								loadimage(&yanhua[4], L"烟花8.png", 435, 391);
								loadimage(&yanhua[5], L"烟花3.png", 300, 270);
								loadimage(&yanhua[6], L"烟花1.png", 541, 396);
								loadimage(&yanhua[7], L"烟花2.png", 325, 344);
								loadimage(&yanhua[8], L"烟花9.png", 525, 485);
								loadimage(&yanhua[9], L"烟花特殊1.5.png", 239, 234);
								loadimage(&yanhua[10], L"烟花特殊1.png", 249, 247);
								loadimage(&yanhua[11], L"烟花特殊2.png", 489, 458);
								loadimage(&yanhua[12], L"烟花特殊3.png", 308, 197);
								loadimage(&yanhua[13], L"烟花特殊4.png", 369, 412);
								loadimage(&back[0], L"烟花背景0.png", 1920, 1080);
								loadimage(&back[1], L"烟花背景.png", 1920, 1080);
								loadimage(&back[2], L"烟花背景2.png", 996, 206);
								loadimage(&back[3], L"烟花背景1.png", 1007, 246);
								PlaySound(NULL, NULL, SND_PURGE);
								wchar_t filename[100];
								swprintf_s(filename, L"胜利.wav");
								PlaySound(filename, NULL, SND_ASYNC | SND_FILENAME | SND_LOOP);
								FlushBatchDraw();
								while (1) {
									BeginBatchDraw();
									cleardevice();
									drawAlphaplus(&back[0], 0, 0, 1920, 1080, 0, 0, 1);
									if (j % 4 == 0) {
										ran = rand() % 4 + 1;
										for (i = 0; i < ran; i++) {
											ran0[i] = rand() % 17 + 1;
											ran1[i] = rand() % 9;
											drawAlphaplus(&yanhua[0],ran0[i]*100,ran1[i]*100,256,256,0,0,1);
										}
										switch (ran2) {
										case(0):
											drawAlphaplus(&yanhua[9], ran20 * 100, ran21 * 100, 239, 234, 0, 0, 1);
											break;
										case(1):
											drawAlphaplus(&yanhua[10], ran20 * 100, ran21 * 100, 249, 247, 0, 0, 1);
											break;
										case(2):
											drawAlphaplus(&yanhua[11], ran20 * 100, ran21 * 100, 489, 458, 0, 0, 1);
											break;
										case(3):
											drawAlphaplus(&yanhua[12], ran20 * 100, ran21 * 100, 308, 197, 0, 0, 1);
											break;
										case(4):
											drawAlphaplus(&yanhua[13], ran20 * 100, ran21 * 100, 369, 412, 0, 0, 1);
											break;
										}
										switch (ran3) {
										case(0):
											drawAlphaplus(&yanhua[9], ran30 * 100, ran31 * 100, 239, 234, 0, 0, 1);
											break;
										case(1):
											drawAlphaplus(&yanhua[10], ran30 * 100, ran31 * 100, 249, 247, 0, 0, 1);
											break;
										case(2):
											drawAlphaplus(&yanhua[11], ran30 * 100, ran31 * 100, 489, 458, 0, 0, 1);
											break;
										case(3):
											drawAlphaplus(&yanhua[12], ran30 * 100, ran31 * 100, 308, 197, 0, 0, 1);
											break;
										case(4):
											drawAlphaplus(&yanhua[13], ran30 * 100, ran31 * 100, 369, 412, 0, 0, 1);
											break;
										}
									}
									if (j % 4 == 1) {
										for (i = 0; i < ran; i++) {
											drawAlphaplus(&yanhua[1], ran0[i]*100-50, ran1[i]*100-50, 359, 338, 0, 0, 1);
										}
										ran2 = rand() % 5;
										ran20 = rand() % 17 + 1;
										ran21 = rand() % 9;
										switch (ran2) {
										case(0):
											drawAlphaplus(&yanhua[9], ran20 * 100, ran21 * 100, 239, 234, 0, 0, 1);
											break;
										case(1):
											drawAlphaplus(&yanhua[10], ran20 * 100, ran21 * 100, 249, 247, 0, 0, 1);
											break;
										case(2):
											drawAlphaplus(&yanhua[11], ran20 * 100, ran21 * 100, 489, 458, 0, 0, 1);
											break;
										case(3):
											drawAlphaplus(&yanhua[12], ran20 * 100, ran21 * 100, 308, 197, 0, 0, 1);
											break;
										case(4):
											drawAlphaplus(&yanhua[13], ran20 * 100, ran21 * 100, 369, 412, 0, 0, 1);
											break;
										}
										switch (ran3) {
										case(0):
											drawAlphaplus(&yanhua[9], ran30 * 100, ran31 * 100, 239, 234, 0, 0, 1);
											break;
										case(1):
											drawAlphaplus(&yanhua[10], ran30 * 100, ran31 * 100, 249, 247, 0, 0, 1);
											break;
										case(2):
											drawAlphaplus(&yanhua[11], ran30 * 000, ran31 * 100, 489, 458, 0, 0, 1);
											break;
										case(3):
											drawAlphaplus(&yanhua[12], ran30 * 100, ran31 * 100, 308, 197, 0, 0, 1);
											break;
										case(4):
											drawAlphaplus(&yanhua[13], ran30 * 100, ran31 * 100, 369, 412, 0, 0, 1);
											break;
										}
									}
									if (j % 4 == 2) {
										for (i = 0; i < ran; i++) {
											drawAlphaplus(&yanhua[2], ran0[i] * 100 -40, ran1[i] * 100 -40,323,305, 0, 0, 1);
										}
										switch (ran2) {
										case(0):
											drawAlphaplus(&yanhua[9], ran20 * 100, ran21 * 100, 239, 234, 0, 0, 1);
											break;
										case(1):
											drawAlphaplus(&yanhua[10], ran20 * 100, ran21 * 100, 249, 247, 0, 0, 1);
											break;
										case(2):
											drawAlphaplus(&yanhua[11], ran20 * 100, ran21 * 100, 489, 458, 0, 0, 1);
											break;
										case(3):
											drawAlphaplus(&yanhua[12], ran20 * 100, ran21 * 100, 308, 197, 0, 0, 1);
											break;
										case(4):
											drawAlphaplus(&yanhua[13], ran20 * 100, ran21 * 100, 369, 412, 0, 0, 1);
											break;
										}
										switch (ran3) {
										case(0):
											drawAlphaplus(&yanhua[9], ran30 * 100, ran31 * 100, 239, 234, 0, 0, 1);
											break;
										case(1):
											drawAlphaplus(&yanhua[10], ran30 * 100, ran31 * 100, 249, 247, 0, 0, 1);
											break;
										case(2):
											drawAlphaplus(&yanhua[11], ran30 * 100, ran31 * 100, 489, 458, 0, 0, 1);
											break;
										case(3):
											drawAlphaplus(&yanhua[12], ran30 * 100, ran31 * 100, 308, 197, 0, 0, 1);
											break;
										case(4):
											drawAlphaplus(&yanhua[13], ran30 * 100, ran31 * 100, 369, 412, 0, 0, 1);
											break;
										}
									}
									if (j % 4 == 3) {
										for (i = 0; i < ran; i++) {
											drawAlphaplus(&yanhua[3], ran0[i] * 100 -80, ran1[i] * 100 -80, 436, 411, 0, 0, 1);
										}
										ran3 = rand() % 5;
										ran30 = rand() % 17 + 1;
										ran31 = rand() % 9;
										switch (ran2) {
										case(0):
											drawAlphaplus(&yanhua[9], ran20 * 100, ran21 * 100, 239, 234, 0, 0, 1);
											break;
										case(1):
											drawAlphaplus(&yanhua[10], ran20 * 100, ran21 * 100, 249, 247, 0, 0, 1);
											break;
										case(2):
											drawAlphaplus(&yanhua[11], ran20 * 100, ran21 * 100, 489, 458, 0, 0, 1);
											break;
										case(3):
											drawAlphaplus(&yanhua[12], ran20 * 100, ran21 * 100, 308, 197, 0, 0, 1);
											break;
										case(4):
											drawAlphaplus(&yanhua[13], ran20 * 100, ran21 * 100, 369, 412, 0, 0, 1);
											break;
										}
										switch (ran3) {
										case(0):
											drawAlphaplus(&yanhua[9], ran30 * 100, ran31 * 100, 239, 234, 0, 0, 1);
											break;
										case(1):
											drawAlphaplus(&yanhua[10], ran30 * 100, ran31 * 100, 249, 247, 0, 0, 1);
											break;
										case(2):
											drawAlphaplus(&yanhua[11], ran30 * 100, ran31 * 100, 489, 458, 0, 0, 1);
											break;
										case(3):
											drawAlphaplus(&yanhua[12], ran30 * 100, ran31 * 100, 308, 197, 0, 0, 1);
											break;
										case(4):
											drawAlphaplus(&yanhua[13], ran30 * 100, ran31 * 100, 369, 412, 0, 0, 1);
											break;
										}
									}
									lp = _kbhit();
									if (lp != 0) {
										dertax = 50;
										dertay = 40;
										if (GetAsyncKeyState(VK_CONTROL)) {
											dertax = dertax + 20;
											dertay = dertay + 16;
										}
										if (GetAsyncKeyState(VK_RIGHT)) {
											dertaD = xmid - x1;
											printf("%d", dertaD);
											if (dertaD > -400) {
												x1 += dertax;
												dx += dertax;
											}
											else {
												if (xnow < 3460) {
													xnow += dertax / 2;
												}
											}
											force = 0;
										}
										if (GetAsyncKeyState(VK_LEFT)) {
											dertaD = xmid - x1;
											printf("%d", dertaD);
											if (dertaD < 500) {
												x1 -= dertax;
												dx -= dertax;
											}
											else {
												if (xnow > 960) {
													xnow -= dertax / 2;
												}
											}
											force = 1;
										}
										if (GetAsyncKeyState(VK_DOWN)) {
											dertaY = ymid - y1;
											printf("%d", dertaY);
											if (dertaY > -160) {
												y1 += dertay;
												dy += dertay;
											}
											else {

											}
										}
										if (GetAsyncKeyState(VK_UP)) {
											dertaY = ymid - y1;
											printf("%d", dertaY);
											if (dertaY < 300) {
												y1 -= dertay;
												dy -= dertay;
											}
											else {

											}
										}
									}
									drawAlphaplus(&back[1], 0, 0, 1920, 1080, 0, 0, 1);
									//右向角色动图
									if (force == 0) {
										if (t % 4 == 0 && t % 2 == 0) {
											drawalpha(&imgH1L, x1 - 28, y1 - 23);
										}
										else if (t % 2 == 1) {
											drawalpha(&imgH1, x1 - 28, y1 - 20);
										}
										else if (t % 4 == 2 && t % 2 == 0) {
											drawalpha(&imgH1R, x1 - 28, y1 - 20);
										}
									}
									//左向角色动图
									if (force == 1) {
										if (t % 4 == 0 && t % 2 == 0) {
											drawalpha(&imgW1L, x1 - 10, y1 - 23);
										}
										else if (t % 2 == 1) {
											drawalpha(&imgW1, x1 - 10, y1 - 23);
										}
										else if (t % 4 == 2 && t % 2 == 0) {
											drawalpha(&imgW1R, x1 - 10, y1 - 23);
										}
									}
									drawAlphaplus(&back[2], 0, 0, 996, 206, 0, 0, 1);
									drawAlphaplus(&back[3], 913, 0, 1007, 246, 0, 0, 1);
									settextstyle(40, 20, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
									swprintf_s(filename, L"恭喜通关");
									outtextxy(90, 100, filename);
									swprintf_s(filename, L"岛上的所有敌对生物都被击败，你获得了离开荒岛的资格");
									outtextxy(20, 950, filename);
									swprintf_s(filename, L"游戏并不会结束，你可以继续在岛屿上生活");
									outtextxy(20, 1000, filename);
									swprintf_s(filename, L"按ESC以退出胜利界面");
									outtextxy(20, 1050, filename);
									FlushBatchDraw();
									t++;
									j++;
									Sleep(150);
									if (GetAsyncKeyState(27)) {
										settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
										break;
									}
								}
								break;
							}
							if (character->life >= 30) {
								settextcolor(YELLOW);
								settextstyle(40, 20, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
								loadimage(&d[15], L"铂金.png", 1920, 1080);
								drawalpha(&d[15], 0, 0);
								swprintf_s(filename, L"%d", character->life * 1000);
								outtextxy(910, 700, filename);
								for (j = 0; j < 3; j++) {
									swprintf_s(filename, L"%d", character->ranking[j]);
									outtextxy(910, 480 + 50 * j, filename);
								}
								FlushBatchDraw();
								if (character->maxscore < character->life * 1000) {
									character->maxscore = character->life * 1000;
								}
								Sleep(5000);
								settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
							}
							else if (character->life >= 20) {
								settextcolor(YELLOW);
								settextstyle(40, 20, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
								loadimage(&d[12], L"黄金.png", 1920, 1080);
								drawalpha(&d[12], 0, 0);
								swprintf_s(filename, L"%d", character->life * 1000);
								outtextxy(910, 700, filename);
								for (j = 0; j < 3; j++) {
									swprintf_s(filename, L"%d", character->ranking[j]);
									outtextxy(910 , 480+50*j, filename);
								}
								FlushBatchDraw();
								if (character->maxscore < character->life * 1000) {
									character->maxscore = character->life * 1000;
								}
								Sleep(5000);
								settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
							}
							else if (character->life > 0) {
								settextcolor(YELLOW);
								settextstyle(40, 20, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
								loadimage(&d[13], L"白银.png", 1920, 1080);
								drawalpha(&d[13], 0, 0);
								swprintf_s(filename, L"%d", character->life * 1000);
								outtextxy(910, 700, filename);
								for (j = 0; j < 3; j++) {
									swprintf_s(filename, L"%d", character->ranking[j]);
									outtextxy(910, 480 + 50 * j, filename);
								}
								FlushBatchDraw();
								if (character->maxscore < character->life * 1000) {
									character->maxscore = character->life * 1000;
								}
								Sleep(5000);
								settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
							}
							break;
						}
					}
					if (character->grasscore > 0) {
						drawalpha(&d[17], 895, 700);
						drawalpha(&d[16], 920, 700);
					}
					//技能提示
					for (im = 0; im < 8; im++) {
						swprintf_s(filename, L"%d:", im);
						drawalpha(&d[105 + im], 800 + 100 * im, 820);
						outtextxy(780 + 100 * im, 840, filename);
					}
					if (time1 == 1) {
						FlushBatchDraw();
						Sleep(800);
						BeginBatchDraw();
						time1 = 0;
					}
					if (j1 > 0) {
						drawalpha(&d[18 - j1+14], 600, 300);
						j1--;
					}
					if (j2 > -2) {
						if (j2 > 0) {
							drawalpha(&d[j2 + 34], 300, 300);
						}
						else drawalpha(&d[j2 + 34], 500, 300);
						j2--;
					}
					if (j3 > 0) {
						drawalpha(&d[47-j3+12], 700, 300);
						j3--;
					}
					if (j4 > 0) {
						drawalpha(&d[59 - j4 + 10], 400, 100);
						j4--;
					}
					if (j5 > 0) {
						drawalpha(&d[69 - j5 + 11], 350, 100);
						j5--;
					}
					if (j6 > 0) {
						drawalpha(&d[80 - j6 + 12], 700, 400);
						j6--;
					}
					if (j7 > 0) {
						drawalpha(&d[92 - j7 + 12], 700, 400);
						j7--;
					}
					swprintf_s(filename, L"按下数字键以进行本次攻击");
					outtextxy(1550, 100, filename);
					swprintf_s(filename, L"按下H键进入教学");
					outtextxy(1550, 150, filename);
					swprintf_s(filename, L"按下T键查看敌人状态");
					outtextxy(1550, 200, filename);
					//printf("  %d %d", character->life, elehead->life);
					FlushBatchDraw();
				}
			}
			BeginBatchDraw();
			break;
		}
		q = q->next;
	}
}
void beginning(ELE* character) {
	int i = 0, j = 0,i1=0,j1=0,ran=1,rank=0,compare,ttt=0,end=0,number,n=0,i2=0;
	MAP* p=NULL, *q=NULL, *h=NULL;
	char ch;
	ExMessage msg;
	IMAGE begin[40],ying[30];
	wchar_t filename[100];
	swprintf_s(filename, L"begin.wav");
	PlaySound(filename, NULL, SND_ASYNC | SND_FILENAME | SND_LOOP);
	settextcolor(WHITE);
	setbkcolor(BLACK);
	flushmessage(EX_KEY);
	loadimage(&begin[16],L"login.png",155, 76);
	loadimage(&begin[17], L"backbag.png", 1920, 1080);
	loadimage(&begin[18], L"paper.png", 409, 473);
	for (i = 0; i < 15; i++) {
			swprintf_s(filename, L"book%d.png", i);
			loadimage(&begin[i], filename, 1920, 1080);
	}
	for (i = 0; i < 12; i++) {
		swprintf_s(filename, L"ying%d.png", i+1);
		loadimage(&ying[i], filename, 837, 777);
	}
	for (i = 0; i < 4; i++) {
		swprintf_s(filename, L"ying%d-.png", i + 5);
		loadimage(&ying[i+12], filename, 837, 777);
	}
	i = 0;
	while (1) {
		Sleep(200);
		if (i1 == 11) {
			ran = rand() % 10;
		}
		BeginBatchDraw();
		printf("%d ", ran);
		cleardevice();
		drawalpha(&begin[17], 0, 0);
		drawalpha(&begin[i], 0, 0);
		swprintf_s(filename, L"按下空格以登录");
		outtextxy(1600, 100, filename);
		if (i1 > 3 && i1 < 8) {
			if (ran % 3 == 0) {
				drawalpha(&ying[i1 + 8], 950, 150+i*7);
			}
			else {
				drawalpha(&ying[i1], 950, 150 + i * 7);
			}
		}
		else {
			drawalpha(&ying[i1], 950, 150 + i * 7);
		}
		drawalpha(&begin[18], 700, 300+i*7);
		drawalpha(&begin[i], 0, 0);
		drawalpha(&begin[16],1700, 200);
		(&begin[16], 800, 200);
		settextstyle(40, 20, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
		swprintf_s(filename, L"荒岛求生");
		outtextxy(800, 200, filename);
		settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
		FlushBatchDraw();
		if (i1 == 11) {
			j1 = 1;
		}
		if (i1 == 0) {
			j1 = 0;
		}
		if (j1 == 0) {
			i1++;
		}
		else i1--;
		if (i == 14) {
			j = 1;
		}
		if (i == 0) {
			j = 0;
		}
		if (j == 0) {
			i++;
		}
		else i--;
		if (peekmessage(&msg, EM_KEY)) {
			if (GetAsyncKeyState(32)) {
				char chi[32]="";
				i = 0;
				wchar_t filename1[10];
				wchar_t file1[10] = L"name";
				FILE* fp,* fp1;
				while (1) {
					InputBox(filename1, 10, L"请输入您的用户名(10字符以内)");
					if (wcscmp(filename1, file1) == 0) {
						swprintf_s(filename, L"不能使用该名称");
						outtextxy(1600, 150, filename);
						FlushBatchDraw();
						BeginBatchDraw();
					}
					else if (wcscmp(filename1, L"") == 0) {
						swprintf_s(filename, L"不能使用非法名称");
						outtextxy(1600, 150, filename);
						FlushBatchDraw();
						BeginBatchDraw();
					}
					else if (wcsncmp(filename1, L" ",1) == 0) {
						swprintf_s(filename, L"不能使用非法名称");
						outtextxy(1600, 150, filename);
						FlushBatchDraw();
						BeginBatchDraw();
					}
					else break;
				}
				int len = WideCharToMultiByte(CP_ACP, 0, filename1,-1, NULL, 0, NULL, NULL);
				character->ch =(char*)malloc(sizeof(char)*(len+1));
				WideCharToMultiByte(CP_ACP, 0,filename1,-1,character->ch,len,NULL, NULL);
				wcscpy(character->name, filename1);
				fp=fopen("name.txt", "r");
				for (i2 = 0; !feof(fp); i2++) {
					if (fgetc(fp) == EOF) {
						end = 1;
					}
					fseek(fp, -1, SEEK_CUR);
					if (end == 1) {
						break;
					}
					fgets(chi, 11, fp);
					int len2;
					len = strlen(character->ch);
					len2 = strlen(chi);
					printf("%s", chi);
					char* chp;
					chp = (char*)malloc(sizeof(char) * (len2));
					char cht[5] = ".txt";
					strncpy_s(chp,sizeof(chp), chi, len2 - 1);
					strcat(chp, cht);
					fp1 = fopen(chp, "r");
					if (strncmp(chi, character->ch, len) == 0 && len2 - len == 1) {
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->xnow);
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->ynow);
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->juqing);
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->battlejq);
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->level);
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->life);
						fgets(chi, 10, fp1);
						sscanf(chi, "%d", &character->molanumber);
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->fruitnum);
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->poisonnum);
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->house);
						for (i = 0; i < 7; i++) {
							fgets(chi, 7, fp1);
							sscanf(chi, "%d", &character->ele[i]);
						}
						for (i = 0; i < 6; i++) {
							fgets(chi, 7, fp1);
							sscanf(chi, "%d", &character->furniture[i]);
						}
						for (i = 0; i < 11; i++) {
							fgets(chi, 7, fp1);//0
							sscanf(chi, "%d", &number);
							if (number == 0) {
								fgets(chi, 7, fp1);
							}
							else {
								for (number; number > 0; number--) {
									p = (MAP*)malloc(sizeof(MAP));
									fgets(chi, 5, fp1);
									sscanf(chi, "%d", &p->x);
									printf("\n%d", p->x);
									fgets(chi, 5, fp1);
									sscanf(chi, "%d", &p->y);
									printf(" %d", p->y);
									p->lei = i;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
								}
								fseek(fp1, 2, SEEK_CUR);
							}
						}
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &character->maxscore);
						if (ttt == 0) {
							character->ranking[0] = character->maxscore;
							ttt += 1;
						}
						else {
							compare = character->maxscore;
							for (i = 0; i < ttt; i++) {
								if (compare > character->ranking[i]) {
									rank = character->ranking[i];
									character->ranking[i] = compare;
									compare = rank;
									if (ttt - i == 1 && i != 2) {
										character->ranking[i + 1] = compare;
									}
								}
								else if (ttt - i == 1 && i != 2) {
									character->ranking[i + 1] = compare;
								}
							}
							if (ttt < 3) {
								ttt += 1;
							}
						}
						for (i = 0; i < 20; i++) {
							fgets(chi, 7, fp1);
							sscanf(chi, "%d", &character->tppoint[i]);
						}
					}
					else {
						for (i = 0; i < 22; i++) {
							fgets(chi, 10, fp1);
						}
						for (i = 0; i < 23; i++) {
							fgets(chi, 30, fp1);
						}
						fgets(chi, 7, fp1);
						sscanf(chi, "%d", &compare);
						if (ttt == 0) {
							character->ranking[0] = compare;
							ttt += 1;
						}
						else {
							for (i = 0; i < ttt; i++) {
								if (compare > character->ranking[i]) {
									rank = character->ranking[i];
									character->ranking[i] = compare;
									compare = rank;
									if (ttt - i == 1 && i!=2) {
										character->ranking[i + 1] = compare;
									}
								}
								else if (ttt - i == 1 && i != 2) {
									character->ranking[i + 1] = compare;
								}
							}
							if (ttt < 3) {
								ttt += 1;
							}
						}
					}
					n++;
					fclose(fp1);
				}
				fclose(fp);
				break;
			}

		}
	}
}
void animalset(int dx, int dy, int xnow, int ynow, MAP* heada, ELE* character) {
	int i = 0, j = 0, mem = 0, xmap, ymap;
	IMAGE d[10];
	MAP* q = NULL;
	q = heada;
	while (q != NULL) {
		if (q->x<xnow + 360 && q->x>xnow - 360 && q->y<ynow + 170 && q->y>ynow - 220 && q->number == 0) {//打印范围内的物品
			wchar_t filename[100];
			loadimage(&d[0], L"果.png", 160, 150);
			loadimage(&d[1], L"土豆.png", 151, 165);
			loadimage(&d[2], L"cao1.png", 175, 128);
			loadimage(&d[3], L"狸猫.png", 126, 140);
			loadimage(&d[4], L"神像.png", 189, 198);
			if (q->lei >= 0) {
				q->img = &d[q->lei];
				drawAlphaplus0(q->img, (q->x - xnow) * 2 + 960, (q->y - ynow) * 2 + 540,0,0,1);
			}
			if (q->x < xnow +30 + dx/2 && q->x > xnow -100 + dx/2 && q->y<ynow +80 + dy/2 && q->y>ynow - 100 + dy/2 && q->number == 0) {
				if (GetAsyncKeyState(70)) {
					if (q->mode == 0) {
						character->fruitnum += 3;
						outtextxy(1700, 300, L"获得3个日落果");
						q->number = 1;
						FlushBatchDraw();
						Sleep(1000);
						BeginBatchDraw();
					}
					if (q->mode == 1) {
						character->molanumber += 10000;
						outtextxy(1700, 300, L"获得10000摩拉");
						q->number = 1;
						FlushBatchDraw();
						Sleep(1000);
						BeginBatchDraw();
					}
					if (q->mode == 2) {
						int num;
						num = q->ele-1;
						character->ele[num] = 1;
						q->number = 1;
						switch (q->ele) {
						case(1):
							swprintf_s(filename, L"取得了水元素");
							break;
						case(2):
							swprintf_s(filename, L"取得了火元素");
							break;
						case(3):
							swprintf_s(filename, L"取得了冰元素");
							break;
						case(4):
							swprintf_s(filename, L"取得了雷元素");
							break;
						case(5):
							swprintf_s(filename, L"取得了风元素");
							break;
						case(6):
							swprintf_s(filename, L"取得了岩元素");
							break;
						case(7):
							swprintf_s(filename, L"取得了草元素");
							break;
						}
						outtextxy(1700, 300, filename);
						FlushBatchDraw();
						Sleep(1000);
						BeginBatchDraw();
					}
					if (q->mode == 3) {
						character->house = 1;
						swprintf_s(filename, L"与这座建筑物接触后");
						outtextxy(1000, 300, filename);
						swprintf_s(filename, L"你获得了在梦境中构建世界的能力");
						outtextxy(1000, 350, filename);
						swprintf_s(filename, L"主世界按下P键以进入梦境中的家园");
						outtextxy(1000, 400, filename);
						swprintf_s(filename, L"按下鼠标左键以确认");
						outtextxy(1100, 500, filename);
						FlushBatchDraw();
						while (1) {
							if (MouseHit()) {
								MOUSEMSG msg = GetMouseMsg();
								if (msg.uMsg == WM_LBUTTONDOWN) {
									break;
								}
							}
						}
						BeginBatchDraw();
					}
				}
			}
		}
		i++;
		q = q->next;
	}
}
MAP* animal() {
	int i=0, j = 0, x[25] = { 6115,6490,8830,1152,2756,5790, 6530,9100,4115,348,7642,4480, 10950,12000,13740,15500,15550, 6000,3914,8430,14450,8280,5180,1332,2643 }, y[25] = { 1820,700,2150,6180,5480,5620, 3676,600,3200,4450,1110,6130, 4800,5780,5930,4400,5760, 6300,5270,2960,5600,950,3450,5620,4800 };
	MAP* head = NULL, * p=NULL, * h, * q = NULL;//输入物品
	for (i = 0; i < 25; i++) {
		p = (MAP*)malloc(sizeof(MAP));
		p->x = x[i];
		p->y = y[i];
		p->number = 0;
		if (i<6) {
			p->lei = 0;//图片信息
			p->ele = 0;//元素
			p->mode = 0;//触发模式
			p->next = NULL;
		}
		else if (i<12) {
			p->ele = 0;
			p->lei = 1;
			p->mode = 0;
			p->next = NULL;
		}
		else if (i < 17) {
			p->ele = 0;
			p->lei = 3;
			p->mode = 0;
			p->next = NULL;
		}
		else if (i <24) {
			p->lei = 2;
			p ->ele = i-16;
			p->mode = 2;
			p->next = NULL;
		}
		else if (i < 25) {
			p->ele = 0;
			p->lei = 4;
			p->mode = 3;
			p->next = NULL;
		}
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
void eating(ELE* character) {
	if (character->life < 40) {
		if (GetAsyncKeyState(69)) {
			if (character->fruitnum > 0) {
				character->fruitnum -= 1;
				character->life += 5;
				if (character->life > 40) {
					character->life = 40;
				}
				outtextxy(1700, 300, L"日落果-1");
				outtextxy(1700, 350, L"生命值+5000");
			}
		}
		if (GetAsyncKeyState(84)) {
			if (character->poisonnum > 0) {
				character->poisonnum -= 1;
				character->life += 10;
				if (character->life > 40) {
					character->life = 40;
				}
				outtextxy(1700, 300, L"生命药水-1");
				outtextxy(1700, 350, L"生命值+10000");
			}
		}
	}
}
void ending(ELE* character) {
	int i = 0, time = 0, len, len2, tt, number1 = 0;
	char cht[5] = ".txt";
	MAP* q;
	char chi[11] = "";
	FILE* fp1;
	fp1 = fopen("name.txt", "r+");///读取名称
	for (i = 0; !feof(fp1); i++) {
		fgets(chi, 11, fp1);
		len = strlen(character->ch);
		len2 = strlen(chi);
		if (strncmp(chi, character->ch, len) == 0 && len2 - len == 1) {
			time = 1;
			break;
		}
	}
	if (time != 1) {
		fclose(fp1);
		fp1 = fopen("name.txt", "a");
		fputs(character->ch, fp1);
		fputs("\n", fp1);
	}
	fclose(fp1);
	strcat(character->ch, cht);
	fp1 = fopen(character->ch, "w");//覆写信息
	fseek(fp1, 0, SEEK_CUR);
	printf("%s", "YES");
	fprintf(fp1, "%d", character->xnow);
	fputs("\n", fp1);
	fprintf(fp1, "%d", character->ynow);
	fputs("\n", fp1);
	fprintf(fp1, "%d", character->juqing);
	fputs("\n", fp1);
	fprintf(fp1, "%d", character->battlejq);
	fputs("\n", fp1);
	fprintf(fp1, "%d", character->level);
	fputs("\n", fp1);
	fprintf(fp1, "%d", character->life);
	fputs("\n", fp1);
	fprintf(fp1, "%d", character->molanumber);
	fputs("\n", fp1);
	fprintf(fp1, "%d", character->fruitnum);
	fputs("\n", fp1);
	fprintf(fp1, "%d", character->poisonnum);
	fputs("\n", fp1);
	fprintf(fp1, "%d", character->house);
	fputs("\n", fp1);
	for (i = 0; i < 7; i++) {
		fprintf(fp1, "%d", character->ele[i]);
		fputs("\n", fp1);
	}
	for (i = 0; i < 6; i++) {
		fprintf(fp1, "%d", character->furniture[i]);
		fputs("\n", fp1);
	}
	for (i = 0; i < 11; i++) {
		number1 = 0;
		q = character->headroom;//0
		while (q != NULL) {
			if (q->lei == i) {
				number1++;
			}
			q = q->next;
		}
		fprintf(fp1, "%d", number1);
		fputs("\n", fp1);
		if (number1 != 0) {
			q = character->headroom;
			while (q != NULL) {
				if (q->lei == i) {
					fprintf(fp1, "%-4d ", q->x);
					fseek(fp1, -1, SEEK_CUR);
					fprintf(fp1, "%-4d ", q->y);
					fseek(fp1, -1, SEEK_CUR);
				}
				q = q->next;
			}
		}
		else fprintf(fp1, "NULL");
		fputs("\n", fp1);
	}
	fprintf(fp1, "%d", character->maxscore);
	for (i = 0; i < 20; i++) {
		fputs("\n", fp1);
		fprintf(fp1, "%d", character->tppoint[i]);
	}
	fclose(fp1);
}
void damage(int xnow,int ynow,ELE* character) {
	if (xnow > 7000 && xnow < 9000 && ynow>2431 && ynow < 3950) {
		if (character->time == 100) {

		}
		else character->time += 1;
	}
	else if (character->time > 0) {
		character->damt = 0;
		character->time -= 2;
		if (character->time < 0) {
			character->time = 0;

		}
	}
	if (character->time == 100) {
		character->damt+=1;
		if (character->damt == 5) {
			character->damt = 0;
			if (character->life > 0) {
				character->life -= 1;
			}
		}
	}
	if (xnow > 7800 && ynow > 4250 && xnow < 9250 && ynow < 6610) {
		if (character->deatht == 100) {

		}
		else character->deatht += 10;
	}
	else if (xnow > 9250 && ynow > 2800 && xnow < 10200 && ynow < 4700) {
		if (character->deatht == 100) {

		}
		else character->deatht += 10;
	}
	else if (xnow < -100 || ynow < -50 || xnow > 16100 || ynow > 6500) {
		if (character->deatht == 100) {

		}
		else character->deatht += 10;
	}
	else if (character->deatht > 0) {
		character->damt = 0;
		character->deatht -= 20;
		if (character->deatht < 0) {
			character->deatht = 0;

		}
	}
	if (character->deatht == 100) {
		character->damt += 1;
		if (character->damt == 5) {
			character->damt = 0;
			if (character->life > 0) {
				character->life -= 10;
			}
		}
	}
}
MAP* tppoint() {
	int i = 0, j = 0, x[15] = {6964,9660}, y[15] = {5220,5070};
	MAP* head = NULL, * p = NULL, * h, * q = NULL;//输入物品
	for (i = 0; i < 2; i++) {
		p = (MAP*)malloc(sizeof(MAP));
		p->x = x[i];
		p->y = y[i];
		p->number = 0;
		p->mode = i;
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
void tp(int xnow, int ynow, int dx, int dy, ELE* character, MAP* tphead) {
	wchar_t filename[100];
	IMAGE d[2];
	MAP* q;
	q = tphead;
	while (q != NULL) {
		if (q->x<xnow + 400 && q->x>xnow - 400 && q->y<ynow + 220 && q->y>ynow - 220 && q->number == 0) {//打印范围内的物品
			loadimage(&d[0], L"传送.png", 73, 100);
			q->img = &d[0];
			drawalpha(q->img, (q->x - xnow) * 2 + 960, (q->y - ynow) * 2 + 540);
		}
		if (q->x < xnow + 30 + dx / 2 && q->x > xnow - 100 + dx / 2 && q->y<ynow + 80 + dy / 2 && q->y>ynow - 100 + dy / 2 && q->number == 0) {
			settextcolor(WHITE);
			flushmessage(EX_KEY);
			swprintf_s(filename, L"按下F键以乘船");
			outtextxy(1650, 300, filename);
			FlushBatchDraw();
			BeginBatchDraw();
			if (GetAsyncKeyState(70)) {
				if (q->mode == 0) {
					character->xnow = 9711;
					character->ynow = 5000;
					swprintf_s(filename, L"已传送至离岛");
					outtextxy(1650, 300, filename);
				}
				if (q->mode == 1) {
					character->xnow = 6860;
					character->ynow = 5130;
					swprintf_s(filename, L"已传送至主岛东海岸");
					outtextxy(1650, 300, filename);
				}
			}
			break;
		}
		q = q->next;
	}
	if (character->life <= 0) {
		character->life = 40;
		character->xnow = 6000;
		character->ynow = 3000;
		swprintf_s(filename, L"已在重生点复活");
		outtextxy(1650, 300, filename);
		if (character->molanumber > 5000) {
			character->molanumber -= 5000;
			swprintf_s(filename, L"消耗5000摩拉复活");
		}
		else {
			swprintf_s(filename, L"消耗%d摩拉复活",character->molanumber);
			character->molanumber = 0;
		}
		outtextxy(1650, 350, filename);
	}
}
LYRIC* lyricset1() {
	int i = 0, x = -31;
	float y[100] = { -11.1,-455.2,-587.4,-631.7,-712.5,-888.9,-1072.8,-1425.8,-1469.9,-1514,-3450,-3824.9,-3847.1,-3891.5,-3936,-3980.5,-4091.7,-4136.2,-4180.5,-4202.7,-4247.1,-4291.6,-4336,-5247.2,-5313.7,-5780.5,-5813.8,-5847.2,-5869.4,-5913.8,-6713.7,-6758.2,-6802.7,-7024.9,-7091.6,-7136.1,-7158.4,-7380.7,-7424.9,-7469.5,-7513.9,-7558.3,-7602.8,-7647.3,-7691.5,-8002.8,-8047.1,-8091.7,-8625.1,-8658.3,-8691.5,-9024.9,-9069.4,-9113.9,-9447.2,-9869.4,-9936.1,-10002.7,-10180.6,-10202.7 };
	float yl1[100] = { -1953.2,-3229.4,-3494.1,-5424.9,-5602.7,-5958.2,-6136.1,-6491.6,-6847.1,-6936.1,-7736.1,-8136.1,-8224.9,-9158.3,-9691.6,-10047.3 },yl2[100] = { -2151.5,-3273.5,-3758.8,-5558.4,-5691.8,-6091.7,-6313.8,-6669.4,-6891.6,-6980.5,-7913.9,-8180.5,-8269.5,-9247.2,-9780.6,-10158.3 };
	LYRIC* head = NULL, * p = NULL, * h, * q = NULL;//输入物品
	for (i = 0; i < 76; i++) {
		p = (LYRIC*)malloc(sizeof(LYRIC));
		p->x1 = x;
		if (i < 60) {
			p->y1 = y[i];
			p->mode = 0;
		}
		else {
			p->yl1 = yl1[i - 60];
			p->yl2 = yl2[i - 60];
			p->mode = 1;
			p->number1 = 0;
			p->number2 = 0;
		}
		p->number = 0;
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
LYRIC* lyricset2() {
	int i = 0, x = -20;
	float y[100]={-58.6,-366.9,-1116.8,-1558.1,-4669.4,-4869.4,-5024.9,-7202.8,-8736.2,-8758.4,-8980.7,-9469.3,-9669.4};
	float yl1[100] = { -2173.7,-2347.1,-2523.5,-2655.8,-2964.7,-3052.9,-3141.1,-3317.6,-4602.7,-4713.8,-4958.4,-5069.5,-8313.7 }, yl2[100] = { -2303,-2435.4,-2589.6,-2700,-3008.8,-3097.1,-3185.3,-3405.8,-4647.2,-4824.9,-5002.7,-5202.7,-8358.4 };
	LYRIC* head = NULL, * p = NULL, * h, * q = NULL;//输入物品
	for (i = 0; i < 26; i++) {
		p = (LYRIC*)malloc(sizeof(LYRIC));
		p->x1 = x;
		if (i < 13) {
			p->y1 = y[i];
			p->mode = 0;
		}
		else {
			p->yl1 = yl1[i - 13];
			p->yl2 = yl2[i - 13];
			p->mode = 1;
			p->number1 = 0;
			p->number2 = 0;
		}
		p->number = 0;
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
LYRIC* lyricset3() {
	int i = 0, x = -10;
	float y[100]={-322.8,-1161.1,-1602.1,-1701.4,-4691.7,-4847.2,-5047.1,-7202.8,-8780.6,-8958.3,-9491.6,-9647.3};
	float yl1[100]={-102.2,-2435.4,-2700,-2964.7,-3052.9,-3141.1,-3317.6,-4536.1,-4780.5,-4891.6,-5158.3,-8313.7}, yl2[100]={-249.2,-2523.5,-2898.5,-3008.8,-3097.1,-3185.3,-3405.8,-4647.2,-4824.9,-5002.7,-5202.7,-8358.4};
	LYRIC* head = NULL, * p = NULL, * h, * q = NULL;//输入物品
	for (i = 0; i < 24; i++) {
		p = (LYRIC*)malloc(sizeof(LYRIC));
		p->x1 = x;
		if (i < 12) {
			p->y1 = y[i];
			p->mode = 0;
		}
		else {
			p->yl1 = yl1[i - 12];
			p->yl2 = yl2[i - 12];
			p->mode = 1;
			p->number1 = 0;
			p->number2 = 0;
		}
		p->number = 0;
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
LYRIC* lyricset4() {
	int i = 0, x = 1;
	float y[100]={-278.6,-888.9,-1249.3,-1337.5,-1425.7,-1646.3,-3450,-3869.5,-3958.3,-4113.7,-4225.1,-4314,-4358.4,-5424.9,- 5469.4,-5513.8,-5558.4,-5780.5,-5813.8,-5847.2,-5869.4,-5913.8,-6847.1,-7091.6,-7136.1,-7158.4,-7736.1,-7780.6,-7825,-7869.5,-8002.8,-8625.1,-8658.3,-8691.5,-8936.2,-9936.1,-10002.7,-10180.6,-10202.7 };
	float yl1[100]={-3229.4,-3494.1,-4025,-4402.7,-5247.2,-5691.8,-6313.8,-6669.4,-6891.6,-6980.5,-7336,-7913.9,-8136.1,-8224.9,-8436.1,-8802.7,-9247.2,-9514,-9780.6,-10047.3 }, yl2[100]={-3273.5,-3758.8,-4069.4,-4491.6,-5380.5,-5736,-6491.6,-6802.7,-6936.1,-7024.9,-7691.5,-7958.3,-8180.5,-8269.5,-8580.5,-8913.9,-9425,-9602.8,-9869.4,-10158.3 };
	LYRIC* head = NULL, * p = NULL, * h, * q = NULL;//输入物品
	for (i = 0; i < 59; i++) {
		p = (LYRIC*)malloc(sizeof(LYRIC));
		p->x1 = x;
		if (i < 39) {
			p->y1 = y[i];
			p->mode = 0;
		}
		else {
			p->yl1 = yl1[i - 39];
			p->yl2 = yl2[i - 39];
			p->mode = 1;
			p->number1 = 0;
			p->number2 = 0;
		}
		p->number = 0;
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
void musicgame(LYRIC* headm1, LYRIC* headm2, LYRIC* headm3, LYRIC* headm4) {
	setaspectratio(1, 1);
	wchar_t filename[100];
	int i = 0,j=0, xnow = 0, score = 0,tick1=0,tick2=0,tick3=0,tick4=0,tt;
	char* ch;
	float currenty1[5] = {0,0,0,0,0}, currenty2[5] = {0,0,0,0,0}, currenty3[5] = {0,0,0,0,0}, currenty4[5] = {0,0,0,0,0}, currentyL1[5] = {0,0,0,0,0}, currentyL2[5] ={0,0,0,0,0}, currentyL3[5] = {0,0,0,0,0}, currentyL4[5] = {0,0,0,0,0}, currentx1 = 0, currentx2 = 0, currentx3 = 0, currentx4 = 0, ttt1[5] = {0,0,0,0,0}, ttt2[5] = {0,0,0,0,0}, ttt3[5] = {0,0,0,0,0}, ttt4[5] = {0,0,0,0,0}, ti1[5] = {0,0,0,0,0}, ti2[5] = {0,0,0,0,0}, ti3[5] = {0,0,0,0,0}, ti4[5] = {0,0,0,0,0}, tj1[5] = {0,0,0,0,0}, tj2[5] = {0,0,0,0,0}, tj3[5] = {0,0,0,0,0}, tj4[5] = {0,0,0,0,0};
	float multi=1,ynow = 0;
	IMAGE d[10],img;
	LYRIC* q1 = headm1, * q2 = headm2, * q3 = headm3, * q4 = headm4,*p1=NULL,*p2=NULL,*p3=NULL,*p4=NULL;
	loadimage(&d[0], L"音符.png", 60, 60);
	loadimage(&d[2], L"音符2.png", 60, 60);
	loadimage(&d[3], L"草原核爆炸.png", 141, 128);
	loadimage(&d[1], L"music.png", 1920, 1080);
	loadimage(&img, L"条.png");
	setfillstyle(BS_DIBPATTERN, NULL, &img);
	//swprintf_s(filename, L"请输入速度倍率(1~3)");
	//outtextxy(1550, 300, filename);
	//FlushBatchDraw();
	//scanf("%f", &multi);
	wchar_t filename1[10];
	InputBox(filename1, 10, L"请输入速度倍率（1~1000）");
	int len = WideCharToMultiByte(CP_ACP, 0, filename1, -1, NULL, 0, NULL, NULL);
	ch = (char*)malloc(sizeof(char) * (len + 1));
	WideCharToMultiByte(CP_ACP, 0, filename1, -1, ch, len, NULL, NULL);
	sscanf(ch, "%f", &multi);
	while (q1 != NULL) {
		q1->y1 = q1->y1 * multi;
		q1->yl1 = q1->yl1 * multi;
		q1->yl2 = q1->yl2 * multi;
		q1 = q1->next;
	}
	while (q2 != NULL) {
		q2->y1 = q2->y1 * multi;
		q2->yl1 = q2->yl1 * multi;
		q2->yl2 = q2->yl2 * multi;
		q2 = q2->next;
	}
	while (q3 != NULL) {
		q3->y1 = q3->y1 * multi;
		q3->yl1 = q3->yl1 * multi;
		q3->yl2 = q3->yl2 * multi;
		q3 = q3->next;
	}
	while (q4 != NULL) {
		q4->y1 = q4->y1 * multi;
		q4->yl1 = q4->yl1 * multi;
		q4->yl2 = q4->yl2 * multi;
		q4 = q4->next;
	}
	settextstyle(40, 20, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
	drawalpha(&d[1], 0, 0);
	swprintf_s(filename, L"3");
	outtextxy(950, 300, filename);
	Sleep(1000);
	BeginBatchDraw();
	cleardevice();
	drawalpha(&d[1], 0, 0);
	swprintf_s(filename, L"2");
	outtextxy(950, 300, filename);
	FlushBatchDraw();
	Sleep(1000);
	BeginBatchDraw();
	cleardevice();
	drawalpha(&d[1], 0, 0);
	swprintf_s(filename, L"1");
	outtextxy(950, 300, filename);
	FlushBatchDraw();
	Sleep(1000);
	cleardevice();
	drawalpha(&d[1], 0, 0);
	swprintf_s(filename, L"开始");
	outtextxy(950, 300, filename);
	FlushBatchDraw();
	Sleep(500);
	PlaySound(NULL, NULL, SND_PURGE);
	swprintf_s(filename, L"%d.wav",21);
	PlaySound(filename, NULL, SND_ASYNC | SND_FILENAME | SND_LOOP);
	while (1) {
		q1 = headm1;
		q2 = headm2;
		q3 = headm3;
		q4 = headm4;
		tt = 0;
		BeginBatchDraw();
		cleardevice();
		drawalpha(&d[1], 0, 0);
		if (ynow <= -10500*multi) {
			loadimage(&d[4], L"音游结算.png", 1920, 1080);
			putimage(0, 0, &d[4]);
			swprintf_s(filename, L"%d",score);
			outtextxy(910, 800, filename);
			FlushBatchDraw();
			settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
			setfillstyle(SOLID_FILL, NULL);
			Sleep(5000);
			PlaySound(NULL, NULL, SND_PURGE);
			break;
		}
		for (i = 0; i < 5; i++) {
			if (ttt1[i] == 1) {
				if (currentyL1[i] == 0) {
					POINT pts[] = { {(currentx1 - xnow) * 2 + 960 - (currenty1[i] - ynow + 200) * 1.02 + 16,(currenty1[i] - ynow) * 2 + 540 - (currenty1[i] - ynow + 200) / 7.1 + 20}, {(currentx1 - xnow) * 2 + 960 - (currenty1[i] - ynow + 200) * 1.02 + 50,(currenty1[i] - ynow) * 2 + 540 - (currenty1[i] - ynow + 200) / 7.1 + 25}, {(currentx1 - xnow) * 2 + 960 + 50, 140 + 26},{(currentx1 - xnow) * 2 + 960 + 16,140 + 20}};
					fillpolygon(pts, 4);
				}
				else {
					POINT pts[] = { {(currentx1 - xnow) * 2 + 960 - (currenty1[i] - ynow + 200) * 1.02 + 16,(currenty1[i] - ynow) * 2 + 540 - (currenty1[i] - ynow + 200) / 7.1 + 20}, {(currentx1 - xnow) * 2 + 960 - (currenty1[i] - ynow + 200) * 1.02 + 50,(currenty1[i] - ynow) * 2 + 540 - (currenty1[i] - ynow + 200) / 7.1 + 25}, {(currentx1 - xnow) * 2 + 960 - (currentyL1[i] - ynow + 200) * 1.02 + 50, 140 + (currentyL1[i] - ynow) * 2 + 400 - (currentyL1[i] - ynow + 200) / 7.1 + 20},{(currentx1 - xnow) * 2 + 960 - (currentyL1[i] - ynow + 200) * 1.02 + 16,140 + (currentyL1[i] - ynow) * 2 + 400 - (currentyL1[i] - ynow + 200) / 7.1 + 20}};
					fillpolygon(pts, 4);
				}
				if (GetAsyncKeyState(83) && ti1[i] == 0 && tj1[i] == 1) {
					drawalpha(&d[3], 506, 751);
					drawalpha(&d[2], 546, 781);
					score += 5;
					swprintf_s(filename, L"perfect");
					tick1 = ynow;
					outtextxy(1650, 300, filename);
				}
			}
		}
		for (i = 0; i < 5; i++) {
			if (ttt2[i] == 1) {
				if (currentyL2[i] == 0) {
					POINT pts[] = { {(currentx2 - xnow) * 2 + 960 - (currenty2[i] - ynow + 200) / 2.14 + 16,(currenty2[i] - ynow) * 2 + 540 + 20}, {(currentx2 - xnow) * 2 + 960 - (currenty2[i] - ynow + 200) / 2.14 + 50,(currenty2[i] - ynow) * 2 + 540 + 25}, {(currentx2 - xnow) * 2 + 960 + 50, 140 + 26},{(currentx2 - xnow) * 2 + 960 + 16,140 + 20} };
					fillpolygon(pts, 4);
				}
				else {
					POINT pts[] = { {(currentx2 - xnow) * 2 + 960 - (currenty2[i] - ynow + 200) / 2.14 + 16,(currenty2[i] - ynow) * 2 + 540 + 20}, {(currentx2 - xnow) * 2 + 960 - (currenty2[i] - ynow + 200) / 2.14 + 50,(currenty2[i] - ynow) * 2 + 540 + 25}, {(currentx2 - xnow) * 2 + 960 - (currentyL2[i] - ynow + 200) / 2.14+50, 140 + (currentyL2[i] - ynow) * 2 + 400 + 20},{(currentx2 - xnow) * 2 + 960 - (currentyL2[i] - ynow + 200) / 2.14 + 16,140 + (currentyL2[i] - ynow) * 2 + 400 + 20}};
					fillpolygon(pts, 4);
				}
				if (GetAsyncKeyState(68) && ti2[i] == 0 && tj2[i] == 1) {
					drawalpha(&d[3], 719, 800);
					drawalpha(&d[2], 759, 830);
					score += 5;
					swprintf_s(filename, L"perfect");
					tick2 = ynow;
					outtextxy(1650, 300, filename);
				}
			}
		}
		for (i = 0; i < 5; i++) {
			if (ttt3[i] == 1) {
				if (currentyL3[i] == 0) {
					POINT pts[] = { {(currentx3 - xnow) * 2 + 960 + (currenty3[i] - ynow + 200) / 2.14 + 12,(currenty3[i] - ynow) * 2 + 540 + 20}, {(currentx3 - xnow) * 2 + 960 + (currenty3[i] - ynow + 200) / 2.14 + 46,(currenty3[i] - ynow) * 2 + 540 + 25}, {(currentx3 - xnow) * 2 + 960 + 46, 140 + 26},{(currentx3 - xnow) * 2 + 960 + 13,140 + 20}};
					fillpolygon(pts, 4);
				}
				else {
					POINT pts[] = { {(currentx3 - xnow) * 2 + 960 + (currenty3[i] - ynow + 200) / 2.14 + 12,(currenty3[i] - ynow) * 2 + 540 + 20}, {(currentx3 - xnow) * 2 + 960 + (currenty3[i] - ynow + 200) / 2.14 + 46,(currenty3[i] - ynow) * 2 + 540 + 25}, {(currentx3 - xnow) * 2 + 960 + (currentyL3[i] - ynow + 200) / 2.14 + 46, 140 + (currentyL3[i] - ynow) * 2 + 400 + 20},{(currentx3 - xnow) * 2 + 960 + (currentyL3[i] - ynow + 200) / 2.14 + 12,140 + (currentyL3[i] - ynow) * 2 + 400 + 20}};
					fillpolygon(pts, 4);
				}
				if (GetAsyncKeyState(74) && ti3[i] == 0 && tj3[i] == 1) {
					drawalpha(&d[3], 1060, 800);
					drawalpha(&d[2], 1101, 830);
					score += 5;
					swprintf_s(filename, L"perfect");
					tick3 = ynow;
					outtextxy(1650, 300, filename);
				}
			}
		}
		for (i = 0; i < 5; i++) {
			if (ttt4[i] == 1) {
				if (currentyL4[i] == 0) {
					POINT pts[] = { {(currentx4 - xnow) * 2 + 960 + (currenty4[i] - ynow + 200) * 1.02 + 9,(currenty4[i] - ynow) * 2 + 540 - (currenty4[i] - ynow + 200) / 7.1 + 20}, {(currentx4 - xnow) * 2 + 960 + (currenty4[i] - ynow + 200) * 1.02 + 43,(currenty4[i] - ynow) * 2 + 540 - (currenty4[i] - ynow + 200) / 7.1 + 25}, {(currentx4 - xnow) * 2 + 960 + 43, 140 + 26},{(currentx4 - xnow) * 2 + 960 + 11,140 + 20}};
					fillpolygon(pts, 4);
				}
				else {
					POINT pts[] = { {(currentx4 - xnow) * 2 + 960 + (currenty4[i] - ynow + 200) * 1.02 + 9,(currenty4[i] - ynow) * 2 + 540 - (currenty4[i] - ynow + 200) / 7.1 + 20}, {(currentx4 - xnow) * 2 + 960 + (currenty4[i] - ynow + 200) * 1.02 + 43,(currenty4[i] - ynow) * 2 + 540 - (currenty4[i] - ynow + 200) / 7.1 + 25}, {(currentx4 - xnow) * 2 + 960 + (currentyL4[i] - ynow + 200) * 1.02 + 43, 140 + (currentyL4[i] - ynow) * 2 + 400 - (currentyL4[i] - ynow + 200) / 7.1 + 20},{(currentx4 - xnow) * 2 + 960 + (currentyL4[i] - ynow + 200) * 1.02 + 9,140 + (currentyL4[i] - ynow) * 2 + 400 - (currentyL4[i] - ynow + 200) / 7.1 + 20}};
					fillpolygon(pts, 4);
				}
				if (GetAsyncKeyState(75) && ti4[i] == 0 && tj4[i] == 1) {
					drawalpha(&d[3], 1274, 750);
					drawalpha(&d[2], 1314, 781);
					score += 5;
					swprintf_s(filename, L"perfect");
					tick4 = ynow;
					outtextxy(1650, 300, filename);
				}
			}
		}
		while (q1 != NULL) {
			if (q1->mode == 0) {
				q1->img = &d[0];
				if (q1->x1<xnow + 360 && q1->x1>xnow - 360 && q1->y1<ynow + 180 && q1->y1>ynow - 200 && q1->number == 0) {//打印范围内的物品
					drawalpha(q1->img, (q1->x1 - xnow) * 2 + 960 - (q1->y1 - ynow + 200) * 1.02, (q1->y1 - ynow) * 2 + 540 - (q1->y1 - ynow + 200) / 7.1);
					if (GetAsyncKeyState(83) && tick1-ynow>15*multi){
						drawalpha(&d[3], 890, 478);
						if (q1->y1 <= ynow + 65 + 35 && q1->y1 > ynow + 85 && q1->number == 0) {
							swprintf_s(filename, L"miss");
							q1->number = 1;
							tick1 = ynow;
							outtextxy(1650, 300, filename);
						}
						if (q1->y1 <= ynow + 105 + 25 && q1->y1 > ynow + 100 && q1->number == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q1->number = 1;
							tick1 = ynow;
							outtextxy(1650, 300, filename);
						}
						if (q1->y1<ynow + 120 + 45 && q1->y1>ynow + 105 + 25 && q1->number == 0) {
							score += 100;
							swprintf_s(filename, L"perfect");
							q1->number = 1;
							tick1 = ynow;
							outtextxy(1650, 300, filename);
						}
						if (q1->y1 < ynow + 140 + 45 && q1->y1 >= ynow + 120 + 45 && q1->number == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q1->number = 1;
							tick1 = ynow;
							outtextxy(1650, 300, filename);
						}
						if (q1->y1 < ynow + 155 + 45 && q1->y1 >= ynow + 140 + 45 && q1->number == 0) {
							swprintf_s(filename, L"miss");
							q1->number = 1;
							tick1 = ynow;
							outtextxy(1650, 300, filename);
						}
					}
					else if (q1->y1 > ynow + 155 + 45 && q1->number == 0) {
						swprintf_s(filename, L"miss");
						q1->number = 1;
						tick1 = ynow;
						outtextxy(1650, 300, filename);
					}
				}
			}
			else {
				q1->img = &d[2];
				if (q1->x1<xnow + 360 && q1->x1>xnow - 360 && q1->yl1<ynow + 180 && q1->yl1>ynow - 200 && q1->number1 == 0) {//打印范围内的物品
					drawalpha(q1->img, (q1->x1 - xnow) * 2 + 960 - (q1->yl1 - ynow + 200) * 1.02, (q1->yl1 - ynow) * 2 + 540 - (q1->yl1 - ynow + 200) / 7.1);
					if (GetAsyncKeyState(83) && tick1 - ynow > 15*multi) {
						drawalpha(&d[3], 890, 478);
						flushmessage();
						tt = 0;
						if (q1->yl1 <= ynow + 65 + 15 && q1->yl1 > ynow + 65 && q1->number1 == 0) {
							swprintf_s(filename, L"miss");
							q1->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty1[i] == q1->yl1) {
									ti1[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty1[i] == 0) {
										ti1[i] = 1;
										break;
									}
								}
							}
						}
						if (q1->yl1 <= ynow + 85 + 25 && q1->yl1 > ynow + 80 && q1->number1 == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q1->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty1[i] == q1->yl1) {
									tj1[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty1[i] == 0) {
										tj1[i] = 1;
										break;
									}
								}
							}
						}
						if (q1->yl1<ynow + 120 + 45 && q1->yl1>ynow + 85 + 25 && q1->number1 == 0) {
							score += 100;
							swprintf_s(filename, L"perfect");
							q1->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty1[i] == q1->yl1) {
									tj1[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty1[i] == 0) {
										tj1[i] = 1;
										break;
									}
								}
							}
						}
						if (q1->yl1 < ynow + 140 + 45 && q1->yl1 >= ynow + 120 + 45 && q1->number1 == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q1->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty1[i] == q1->yl1) {
									tj1[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty1[i] == 0) {
										tj1[i] = 1;
										break;
									}
								}
							}
						}
						if (q1->yl1 < ynow + 155 + 45 && q1->yl1 >= ynow + 140 + 45 && q1->number1 == 0) {
							swprintf_s(filename, L"miss");
							q1->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty1[i] == q1->yl1) {
									ti1[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty1[i] == 0) {
										ti1[i] = 1;
										break;
									}
								}
							}
						}
					}
					else if (q1->yl1 > ynow + 155 + 45 && q1->number1 == 0) {
						swprintf_s(filename, L"miss");
						q1->number1 = 1;
						outtextxy(1650, 300, filename);
						for (i = 0; i < 5; i++) {
							if (currenty1[i] == q1->yl1) {
								ti1[i] = 1;
								tt = 1;
								break;
							}
						}
						if (tt != 1) {
							for (i = 0; i < 5; i++) {
								if (currenty1[i] == 0) {
									ti1[i] = 1;
									break;
								}
							}
						}
					}
					tt = 0;
					for (i = 0; i < 5; i++) {
						if (currenty1[i] == q1->yl1) {
							currentx1 = q1->x1;
							currenty1[i] = q1->yl1;
							ttt1[i] = 1;
							tt = 1;
							break;
						}
					}
					if (tt != 1) {
						for (i = 0; i < 5; i++) {
							if (currenty1[i] == 0) {
								currentx1 = q1->x1;
								currenty1[i] = q1->yl1;
								ttt1[i] = 1;
								break;
							}
						}
					}
					printf("  %d\n", i);
				}
				if (q1->x1<xnow + 360 && q1->x1>xnow - 360 && q1->yl2<ynow + 180 && q1->yl2>ynow - 200 && q1->number2 == 0) {//打印范围内的物品
					drawalpha(q1->img, (q1->x1 - xnow) * 2 + 960 - (q1->yl2 - ynow + 200) * 1.02, (q1->yl2 - ynow) * 2 + 540 - (q1->yl2 - ynow + 200) / 7.1);
					for (i = 0; i < 5; i++) {
						if (currenty1[i] == q1->yl1) {
							currentyL1[i] = q1->yl2;
						}
					}
					printf("%d\n", i);
					if (q1->yl2 < ynow + 140 + 45 && q1->yl2 >= ynow + 120 + 45 && q1->number2 == 0) {
						drawalpha(&d[3], 890, 478);
						score += 50;
						swprintf_s(filename, L"nice");
						q1->number2 = 1;
						outtextxy(1650, 300, filename);
						tick1 = ynow;
						for (i = 0; i < 5; i++) {
							if (currenty1[i] == q1->yl1) {
								ttt1[i] = 0;
								ti1[i] = 0;
							    tj1[i] = 0;
								currenty1[i] = 0;
								currentyL1[i] = 0;
							}
						}
					}
				}
			}
			p1 = q1;
			q1 = q1->next;
		}
		while (q2 != NULL) {
			if (q2->mode == 0) {
				q2->img = &d[0];
				if (q2->x1<xnow + 360 && q2->x1>xnow - 360 && q2->y1<ynow + 180 && q2->y1>ynow - 200 && q2->number == 0) {//打印范围内的物品
					drawalpha(q2->img, (q2->x1 - xnow) * 2 + 960 - (q2->y1 - ynow + 200) / 2.14, (q2->y1 - ynow) * 2 + 540);
					if (GetAsyncKeyState(68) && tick2 - ynow > 15*multi) {
						drawalpha(&d[3], 890, 478);
						if (q2->y1 <= ynow + 110 && q2->y1 > ynow + 95 && q2->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							swprintf_s(filename, L"miss");
							outtextxy(1650, 300, filename);
							tick2 = ynow;
							q2->number = 1;
						}
						if (q2->y1 <= ynow + 85 + 45 && q2->y1 > ynow + 85 + 25 && q2->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							score += 50;
							swprintf_s(filename, L"nice");
							outtextxy(1650, 300, filename);
							tick2 = ynow;
							q2->number = 1;
						}
						if (q2->y1<ynow + 120 + 45 && q2->y1>ynow + 85 + 45 && q2->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							score += 100;
							swprintf_s(filename, L"perfect");
							outtextxy(1650, 300, filename);
							tick2 = ynow;
							q2->number = 1;
						}
						if (q2->y1<ynow + 140 + 45 && q2->y1>ynow + 120 + 45 && q2->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							score += 50;
							swprintf_s(filename, L"nice");
							outtextxy(1650, 300, filename);
							tick2 = ynow;
							q2->number = 1;
						}
						if (q2->y1<ynow + 155 + 45 && q2->y1>ynow + 140 + 45 && q2->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							swprintf_s(filename, L"miss");
							outtextxy(1650, 300, filename);
							tick2 = ynow;
							q2->number = 1;
						}
					}
					else if (q2->y1 >= ynow + 155 + 45 && q2->number == 0) {
						swprintf_s(filename, L"miss");
						outtextxy(1650, 300, filename);
						tick2 = ynow;
						q2->number = 1;
					}
				}
			}
			else {
				q2->img = &d[2];
				if (q2->x1<xnow + 360 && q2->x1>xnow - 360 && q2->yl1<ynow + 180 && q2->yl1>ynow - 200 && q2->number1 == 0) {//打印范围内的物品
					drawalpha(q2->img, (q2->x1 - xnow) * 2 + 960 - (q2->yl1 - ynow + 200)/2.14, (q2->yl1 - ynow) * 2 + 540);
					if (GetAsyncKeyState(68) && tick2-ynow>15 * multi) {
						drawalpha(&d[3], 890, 478);
						tt = 0;
						if (q2->yl1 <= ynow + 110 && q2->yl1 > ynow + 95 && q2->number1 == 0) {
							swprintf_s(filename, L"miss");
							q2->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty2[i] == q2->yl1) {
									ti2[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty2[i] == 0) {
										ti2[i] = 1;
										break;
									}
								}
							}
						}
						if (q2->yl1 <= ynow + 85 + 45 && q2->yl1 > ynow + 85+25 && q2->number1 == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q2->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty2[i] == q2->yl1) {
									tj2[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty2[i] == 0) {
										tj2[i] = 1;
										break;
									}
								}
							}
						}
						if (q2->yl1<ynow + 120 + 45 && q2->yl1>ynow + 85 + 45 && q2->number1 == 0) {
							score += 100;
							swprintf_s(filename, L"perfect");
							q2->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty2[i] == q2->yl1) {
									tj2[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty2[i] == 0) {
										tj2[i] = 1;
										break;
									}
								}
							}
						}
						if (q2->yl1 < ynow + 140 + 45 && q2->yl1 >= ynow + 120 + 45 && q2->number1 == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q2->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty2[i] == q2->yl1) {
									tj2[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty2[i] == 0) {
										tj2[i] = 1;
										break;
									}
								}
							}
						}
						if (q2->yl1 < ynow + 155 + 45 && q2->yl1 >= ynow + 140 + 45 && q2->number1 == 0) {
							swprintf_s(filename, L"miss");
							q2->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty2[i] == q2->yl1) {
									ti2[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty2[i] == 0) {
										ti2[i] = 1;
										break;
									}
								}
							}
						}
					}
					else if (q2->yl1 > ynow + 155 + 45 && q2->number1 == 0) {
						swprintf_s(filename, L"miss");
						q2->number1 = 1;
						outtextxy(1650, 300, filename);
						for (i = 0; i < 5; i++) {
							if (currenty2[i] == q2->yl1) {
								ti2[i] = 1;
								tt = 1;
								break;
							}
						}
						if (tt != 1) {
							for (i = 0; i < 5; i++) {
								if (currenty2[i] == 0) {
									ti2[i] = 1;
									break;
								}
							}
						}
					}
					tt = 0;
					for (i = 0; i < 5; i++) {
						if (currenty2[i] == q2->yl1) {
							currentx2 = q2->x1;
							currenty2[i] = q2->yl1;
							ttt2[i] = 1;
							tt = 1;
							break;
						}
					}
					if (tt != 1) {
						for (i = 0; i < 5; i++) {
							if (currenty2[i] == 0) {
								currentx2 = q2->x1;
								currenty2[i] = q2->yl1;
								ttt2[i] = 1;
								break;
							}
						}
					}
					printf("  %d\n", i);
				}
				if (q2->x1<xnow + 360 && q2->x1>xnow - 360 && q2->yl2<ynow + 180 && q2->yl2>ynow - 200 && q2->number2 == 0) {//打印范围内的物品
					drawalpha(q2->img, (q2->x1 - xnow) * 2 + 960 - (q2->yl2 - ynow + 200)/2.14, (q2->yl2 - ynow) * 2 + 540);
					for (i = 0; i < 5; i++) {
						if (currenty2[i] == q2->yl1) {
							currentyL2[i] = q2->yl2;
							break;
						}
					}
					printf("%d\n", i);
					if (q2->yl2 < ynow + 140 + 45 && q2->yl2 >= ynow + 130 + 15 && q2->number2 == 0) {
						drawalpha(&d[3], 890, 478);
						score += 50;
						swprintf_s(filename, L"nice");
						tick2 = ynow;
						q2->number2 = 1;
						outtextxy(1650, 300, filename);
						for (i = 0; i < 5; i++) {
							if (currenty2[i] == q2->yl1) {
								ttt2[i] = 0;
								ti2[i] = 0;
								tj2[i] = 0;
								currenty2[i] = 0;
								currentyL2[i] = 0;
							}
						}
					}
				}
			}
			q2 = q2->next;
		}
		while (q3 != NULL) {
			if (q3->mode == 0) {
				q3->img = &d[0];
				if (q3->x1<xnow + 360 && q3->x1>xnow - 360 && q3->y1<ynow + 180 && q3->y1>ynow - 200 && q3->number == 0) {//打印范围内的物品
					drawalpha(q3->img, (q3->x1 - xnow) * 2 + 960 + (q3->y1 - ynow + 200) / 2.14, (q3->y1 - ynow) * 2 + 540);
					if (GetAsyncKeyState(74) && tick3-ynow>15 * multi) {
						drawalpha(&d[3], 890, 478);
						if (q3->y1 <= ynow + 110 && q3->y1 > ynow + 95 && q3->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							swprintf_s(filename, L"miss");
							outtextxy(1650, 300, filename);
							tick3 = ynow;
							q3->number = 1;
						}
						if (q3->y1 <= ynow + 85 + 45 && q3->y1 > ynow + 85 + 25 && q3->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							score += 50;
							swprintf_s(filename, L"nice");
							outtextxy(1650, 300, filename);
							tick3 = ynow;
							q3->number = 1;
						}
						if (q3->y1<ynow + 120 + 45 && q3->y1>ynow + 85 + 45 && q3->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							score += 100;
							swprintf_s(filename, L"perfect");
							outtextxy(1650, 300, filename);
							tick3 = ynow;
							q3->number = 1;
						}
						if (q3->y1<ynow + 140 + 45 && q3->y1>ynow + 120 + 45 && q3->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							score += 50;
							swprintf_s(filename, L"nice");
							outtextxy(1650, 300, filename);
							tick3 = ynow;
							q3->number = 1;
						}
						if (q3->y1<ynow + 155 + 45 && q3->y1>ynow + 140 + 45 && q3->number == 0) {//q2->x1<xnow - 50 && q2->x1>xnow - 100 && 
							swprintf_s(filename, L"miss");
							outtextxy(1650, 300, filename);
							tick3 = ynow;
							q3->number = 1;
						}
					}
					else if (q3->y1 >= ynow + 155 + 45 && q3->number == 0) {
						swprintf_s(filename, L"miss");
						outtextxy(1650, 300, filename);
						tick3 = ynow;
						q3->number = 1;
					}
				}
			}
			else {
				q3->img = &d[2];
				if (q3->x1<xnow + 360 && q3->x1>xnow - 360 && q3->yl1<ynow + 180 && q3->yl1>ynow - 200 && q3->number1 == 0) {//打印范围内的物品
					drawalpha(q3->img, (q3->x1 - xnow) * 2 + 960 + (q3->yl1 - ynow + 200) / 2.14, (q3->yl1 - ynow) * 2 + 540);
					if (GetAsyncKeyState(74) && tick3-ynow>15 * multi) {
						drawalpha(&d[3], 890, 478);
						tt = 0;
						if (q3->yl1 <= ynow + 110 && q3->yl1 > ynow + 95 && q3->number1 == 0) {
							swprintf_s(filename, L"miss");
							q3->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty3[i] == q3->yl1) {
									ti3[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty3[i] == 0) {
										ti3[i] = 1;
										break;
									}
								}
							}
						}
						if (q3->yl1 <= ynow + 85 + 45 && q3->yl1 > ynow + 85 + 25 && q3->number1 == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q3->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty3[i] == q3->yl1) {
									tj3[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty3[i] == 0) {
										tj3[i] = 1;
										break;
									}
								}
							}
						}
						if (q3->yl1<ynow + 120 + 45 && q3->yl1>ynow + 85 + 45 && q3->number1 == 0) {
							score += 100;
							swprintf_s(filename, L"perfect");
							q3->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty3[i] == q3->yl1) {
									tj3[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty3[i] == 0) {
										tj3[i] = 1;
										break;
									}
								}
							}
						}
						if (q3->yl1 < ynow + 140 + 45 && q3->yl1 >= ynow + 120 + 45 && q3->number1 == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q3->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty3[i] == q3->yl1) {
									tj3[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty3[i] == 0) {
										tj3[i] = 1;
										break;
									}
								}
							}
						}
						if (q3->yl1 < ynow + 155 + 45 && q3->yl1 >= ynow + 140 + 45 && q3->number1 == 0) {
							swprintf_s(filename, L"miss");
							q3->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty3[i] == q3->yl1) {
									ti3[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty3[i] == 0) {
										ti3[i] = 1;
										break;
									}
								}
							}
						}
					}
					else if (q3->yl1 > ynow + 155 + 45 && q3->number1 == 0) {
						swprintf_s(filename, L"miss");
						q3->number1 = 1;
						outtextxy(1650, 300, filename);
						for (i = 0; i < 5; i++) {
							if (currenty3[i] == q3->yl1) {
								ti3[i] = 1;
								tt = 1;
								break;
							}
						}
						if (tt != 1) {
							for (i = 0; i < 5; i++) {
								if (currenty3[i] == 0) {
									ti3[i] = 1;
									break;
								}
							}
						}
					}
					tt = 0;
					for (i = 0; i < 5; i++) {
						if (currenty3[i] == q3->yl1) {
							currentx3 = q3->x1;
							currenty3[i] = q3->yl1;
							ttt3[i] = 1;
							tt = 1;
							break;
						}
					}
					if (tt != 1) {
						for (i = 0; i < 5; i++) {
							if (currenty3[i] == 0) {
								currentx3 = q3->x1;
								currenty3[i] = q3->yl1;
								ttt3[i] = 1;
								break;
							}
						}
					}
					printf("a%d\n", i);
				}
				//printf("b%d\n", i);
				if (q3->x1<xnow + 360 && q3->x1>xnow - 360 && q3->yl2<ynow + 180 && q3->yl2>ynow - 200 && q3->number2 == 0) {//打印范围内的物品
					drawalpha(q3->img, (q3->x1 - xnow) * 2 + 960 + (q3->yl2 - ynow + 200) / 2.14, (q3->yl2 - ynow) * 2 + 540);
					for (i = 0; i < 5; i++) {
						if (currenty3[i] == q3->yl1) {
							currentyL3[i] = q3->yl2;
							break;
						}
					}
					//printf("c%d\n", i);
					if (q3->yl2 < ynow + 140 + 45 && q3->yl2 >= ynow + 130 + 15 && q3->number2 == 0) {
						drawalpha(&d[3], 890, 478);
						score += 50;
						swprintf_s(filename, L"nice");
						tick3 = ynow;
						q3->number2 = 1;
						outtextxy(1650, 300, filename);
						for (i = 0; i < 5; i++) {
							if (currenty3[i] == q3->yl1) {
								ttt3[i] = 0;
								ti3[i] = 0;
								tj3[i] = 0;
								currenty3[i] = 0;
								currentyL3[i] = 0;
								break;
							}
						}
					}
				}
			}
			q3 = q3->next;
		}
		while (q4 != NULL) {
			if (q4->mode == 0) {
				q4->img = &d[0];
				if (q4->x1<xnow + 360 && q4->x1>xnow - 360 && q4->y1<ynow + 180 && q4->y1>ynow - 200 && q4->number == 0) {//打印范围内的物品
					drawalpha(q4->img, (q4->x1 - xnow) * 2 + 960 + (q4->y1 - ynow + 200) * 1.02, (q4->y1 - ynow) * 2 + 540 - (q4->y1 - ynow + 200) / 7.1);
					if (GetAsyncKeyState(75) && tick4-ynow>15 * multi) {
						drawalpha(&d[3], 890, 478);
						if (q4->y1 <= ynow + 65 + 15 && q4->y1 > ynow + 65 && q4->number == 0) {
							swprintf_s(filename, L"miss");
							tick4 = ynow;
							q4->number = 1;
							outtextxy(1650, 300, filename);
						}
						if (q4->y1 <= ynow + 85 + 15 && q4->y1 > ynow + 80 && q4->number == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							tick4 = ynow;
							q4->number = 1;
							outtextxy(1650, 300, filename);
						}
						if (q4->y1<ynow + 120 + 45 && q4->y1>ynow + 85 + 15 && q4->number == 0) {
							score += 100;
							swprintf_s(filename, L"perfect");
							tick4 = ynow;
							q4->number = 1;
							outtextxy(1650, 300, filename);
						}
						if (q4->y1 < ynow + 140 + 45 && q4->y1 >= ynow + 120 + 15 && q4->number == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							tick4 = ynow;
							q4->number = 1;
							outtextxy(1650, 300, filename);
						}
						if (q4->y1 < ynow + 155 + 45 && q4->y1 >= ynow + 140 + 15 && q4->number == 0) {
							swprintf_s(filename, L"miss");
							tick4 = ynow;
							q4->number = 1;
							outtextxy(1650, 300, filename);
						}
					}
					else if (q4->y1 > ynow + 155 + 45 && q4->number == 0) {
						swprintf_s(filename, L"miss");
						tick4 = ynow;
						q4->number = 1;
						outtextxy(1650, 300, filename);
					}
				}
			}
			else {
				q4->img = &d[2];
				if (q4->x1<xnow + 360 && q4->x1>xnow - 360 && q4->yl1<ynow + 180 && q4->yl1>ynow - 200 && q4->number1 == 0) {//打印范围内的物品
					drawalpha(q4->img, (q4->x1 - xnow) * 2 + 960 + (q4->yl1 - ynow + 200) * 1.02, (q4->yl1 - ynow) * 2 + 540 - (q4->yl1 - ynow + 200) / 7.1);
					if (GetAsyncKeyState(75) && tick4-ynow>15 * multi) {
						drawalpha(&d[3], 890, 478);
						tt = 0;
						if (q4->yl1 <= ynow + 65 + 35 && q4->yl1 > ynow + 85 && q4->number1 == 0) {
							swprintf_s(filename, L"miss");
							q4->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty4[i] == q4->yl1) {
									ti4[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty4[i] == 0) {
										ti4[i] = 1;
										break;
									}
								}
							}
						}
						if (q4->yl1 <= ynow + 105 + 25 && q4->yl1 > ynow + 100 && q4->number1 == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q4->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty4[i] == q4->yl1) {
									tj4[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty4[i] == 0) {
										tj4[i] = 1;
										break;
									}
								}
							}
						}
						if (q4->yl1<ynow + 120 + 45 && q4->yl1>ynow + 105 + 25 && q4->number1 == 0) {
							score += 100;
							swprintf_s(filename, L"perfect");
							q4->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty4[i] == q4->yl1) {
									tj4[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty4[i] == 0) {
										tj4[i] = 1;
										break;
									}
								}
							}
						}
						if (q4->yl1 < ynow + 140 + 45 && q4->yl1 >= ynow + 120 + 45 && q4->number1 == 0) {
							score += 50;
							swprintf_s(filename, L"nice");
							q4->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty4[i] == q4->yl1) {
									tj4[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty4[i] == 0) {
										tj4[i] = 1;
										break;
									}
								}
							}
						}
						if (q4->yl1 < ynow + 155 + 45 && q4->yl1 >= ynow + 140 + 45 && q4->number1 == 0) {
							swprintf_s(filename, L"miss");
							q4->number1 = 1;
							outtextxy(1650, 300, filename);
							for (i = 0; i < 5; i++) {
								if (currenty4[i] == q4->yl1) {
									ti4[i] = 1;
									tt = 1;
									break;
								}
							}
							if (tt != 1) {
								for (i = 0; i < 5; i++) {
									if (currenty4[i] == 0) {
										ti4[i] = 1;
										break;
									}
								}
							}
						}
					}
					else if (q4->yl1 > ynow + 155 + 45 && q4->number1 == 0) {
						swprintf_s(filename, L"miss");
						q4->number1 = 1;
						outtextxy(1650, 300, filename);
						for (i = 0; i < 5; i++) {
							if (currenty4[i] == q4->yl1) {
								ti4[i] = 1;
								tt = 1;
								break;
							}
						}
						if (tt != 1) {
							for (i = 0; i < 5; i++) {
								if (currenty4[i] == 0) {
									ti4[i] = 1;
									break;
								}
							}
						}
					}
					tt = 0;
					for (i = 0; i < 5; i++) {
						if (currenty4[i] == q4->yl1) {
							currentx4 = q4->x1;
							currenty4[i] = q4->yl1;
							ttt4[i] = 1;
							tt = 1;
							break;
						}
					}
					if (tt != 1) {
						for (i = 0; i < 5; i++) {
							if (currenty4[i] == 0) {
								currentx4 = q4->x1;
								currenty4[i] = q4->yl1;
								ttt4[i] = 1;
								break;
							}
						}
					}
				}
				if (q4->x1<xnow + 360 && q4->x1>xnow - 360 && q4->yl2<ynow + 180 && q4->yl2>ynow - 200 && q4->number2 == 0) {//打印范围内的物品
					drawalpha(q4->img, (q4->x1 - xnow) * 2 + 960 + (q4->yl2 - ynow + 200) * 1.02, (q4->yl2 - ynow) * 2 + 540 - (q4->yl2 - ynow + 200) / 7.1);
					for (i = 0; i < 5; i++) {
						if (currenty4[i] == q4->yl1) {
							currentyL4[i] = q4->yl2;
						}
					}
					if (q4->yl2 < ynow + 140 + 45 && q4->yl2 >= ynow + 120 + 45 && q4->number2 == 0) {
						drawalpha(&d[3], 890, 478);
						score += 50;
						swprintf_s(filename, L"nice");
						tick4 = ynow;
						q4->number2 = 1;
						outtextxy(1650, 300, filename);
						for (i = 0; i < 5; i++) {
							if (currenty4[i] == q4->yl1) {
								ttt4[i] = 0;
								ti4[i] = 0;
								tj4[i] = 0;
								currenty4[i] = 0;
								currentyL4[i] = 0;
							}
						}
					}
				}
			}
			q4 = q4->next;
		}
		swprintf_s(filename, L"%d", score);
		outtextxy(1650, 200, filename);
		FlushBatchDraw();
		ynow -= 1.55*multi;//1.952//2.036
		Sleep(0);
	}
}
void park(ELE* character) {
	int t = 0, i = 0,j1=0,j2=0,j3=0,j4=0,j5=0,j6=0, value1=0, xmid = 960, dertaD, lp, dertaY, ymid = 540, force = 0, dertax = 50, dertay = 50, xnow = 960, ynow = 540, x1 = 800, y1 = 400, dx = 0, dy = 0;
	ExMessage msg;
	wchar_t filename[100];
	IMAGE imgW1L, imgW1R, imgW1, imgH1L, imgH1R, imgH1,mapb[9],stage[20],dragon,windslime[41],cao[11],xue[42],xue1[42],lei[35],dong;
	loadimage(&imgW1R, L"./LRIGHT.png", 196, 294);
	loadimage(&imgW1L, L"./LLEFT.png", 202, 294);
	loadimage(&imgW1, L"./LNORMAL.png", 196, 296);
	loadimage(&imgH1R, L"./RRIGHT.png", 202, 294);
	loadimage(&imgH1L, L"./RLEFT.png", 192, 300);
	loadimage(&imgH1, L"./RNORMAL.png", 198, 296);
	loadimage(&dragon, L"骷髅龙.png", 423, 290);
	loadimage(&dong, L"洞.png", 403, 220);
	setbkcolor(LIGHTCYAN);
	for (i = 0; i < 9; i++) {
		swprintf_s(filename, L"家园外景%d.png", i);
		loadimage(&mapb[i],filename, 4423, 1080);//2949
	}
	for (i = 0; i < 20; i++) {
		swprintf_s(filename, L"花园%d.png", i);
		loadimage(&stage[i], filename, 1130, 726);
	}
	for (i = 0; i < 41; i++) {
		swprintf_s(filename, L"风史莱姆%d.png", i);
		loadimage(&windslime[i], filename, 500, 747);
	}
	for (i = 0; i < 11; i++) {
		swprintf_s(filename, L"草%d.png", i);
		loadimage(&cao[i], filename, 868, 340);
	}
	for (i = 0; i < 42; i++) {
		swprintf_s(filename, L"雪山%d.png", i);
		loadimage(&xue[i], filename, 837, 533);
	}
	for (i = 0; i < 42; i++) {
		swprintf_s(filename, L"下雪%d.png", i);
		loadimage(&xue1[i], filename, 804, 485);
	}
	for (i = 0; i < 35; i++) {
		swprintf_s(filename, L"雷史莱姆%d.png", i);
		loadimage(&lei[i], filename, 250, 390);
	}
	i = 0;
	while (1) {
		if (GetAsyncKeyState(27)) {
			break;
		}
		if (GetAsyncKeyState(83)) {
			room(character);
		}
		BeginBatchDraw();
		cleardevice();
		backpack(character);
		if (xnow > 465 && xnow < 1305 && ynow>135 && ynow < 655) {
			swprintf_s(filename,L"按下F键以交互");
			outtextxy(1650, 300, filename);
			if (GetAsyncKeyState(70)) {
				if (value1 == 0) {
					value1 = 43;
				}
				else {
					value1 = 0;
				}
			}
		}
		if (i == 9) {
			i = 0;
		}
		drawAlphaplus(&mapb[i], 0, 0, 1920, 1080,xnow-960, ynow-540);
		if (j1 == 20) {
			j1 = 0;
		}
		if (character->furniture[0] == 1) {
			drawAlphaplus(&stage[j1], (2486 - xnow) + 960, (137 - ynow) + 540, 1130, 726, 0, 0, 1);
		}
		if (j2 == 41) {
			j2 = 0;
		}
		if (j5 == 35) {
			j5 = 0;
		}
		if (character->furniture[1] == 1) {
			drawAlphaplus(&windslime[j2], (3866 - xnow) + 960, (-2 - ynow) + 540, 500, 747, 0, 0, 1);
			drawAlphaplus(&lei[j5], (3610 - xnow) + 960, (141 - ynow) + 540, 250, 390, 0, 0, 1);
		}
		if (j3 == 11) {
			j3 = 0;
		}
		if (character->furniture[2] == 1) {
			drawAlphaplus(&cao[j3], (459 - xnow) + 960, (665 - ynow) + 540, 868, 340, 0, 0, 1);
		}
		if (j4 == 42) {
			j4 = 0;
		}
		if (value1 > 0 && character->furniture[3] == 1) {
			drawAlphaplus(&xue1[j4], (495 - xnow) + 960, (134 - ynow) + 540, 804, 485, 0, 0, 1);
			value1--;
		}
		else if(character->furniture[3] == 1) {
			drawAlphaplus(&xue[j4], (465 - xnow) + 960, (135 - ynow) + 540, 837, 533, 0, 0, 1);
		}
		if (character->furniture[4] == 1) {
			drawAlphaplus(&dragon, (2520 - xnow) + 960, (673 - ynow) + 540, 423, 290, 0, 0, 1);
		}
		if (character->furniture[5] == 1) {
			drawAlphaplus(&dong, (40 - xnow) + 960, (320 - ynow) + 540, 403, 220, 0, 0, 1);
		}
		//方向控制代码
		lp = _kbhit();
		if (lp != 0) {
			dertax = 50;
			dertay = 40;
			if (GetAsyncKeyState(VK_CONTROL)) {
				dertax = dertax + 20;
				dertay = dertay + 16;
			}
			if (GetAsyncKeyState(VK_RIGHT)) {
				dertaD = xmid - x1;
				printf("%d", dertaD);
				if (dertaD > -400) {
					x1 += dertax;
					dx += dertax;
				}
				else {
					if (xnow < 3460) {
						xnow += dertax / 2;
					}
				}
				force = 0;
			}
			if (GetAsyncKeyState(VK_LEFT)) {
				dertaD = xmid - x1;
				printf("%d", dertaD);
				if (dertaD < 500) {
					x1 -= dertax;
					dx -= dertax;
				}
				else {
					if (xnow >960) {
						xnow -= dertax / 2;
					}
				}
				force = 1;
			}
			if (GetAsyncKeyState(VK_DOWN)) {
				dertaY = ymid - y1;
				printf("%d", dertaY);
				if (dertaY > -160) {
					y1 += dertay;
					dy += dertay;
				}
				else {

				}
			}
			if (GetAsyncKeyState(VK_UP)) {
				dertaY = ymid - y1;
				printf("%d", dertaY);
				if (dertaY < 300) {
					y1 -= dertay;
					dy -= dertay;
				}
				else {

				}
			}
		}
		//右向角色动图
		if (force == 0) {
			if (t % 4 == 0 && t % 2 == 0) {
				drawalpha(&imgH1L, x1 - 28, y1 - 23);
			}
			else if (t % 2 == 1) {
				drawalpha(&imgH1, x1 - 28, y1 - 20);
			}
			else if (t % 4 == 2 && t % 2 == 0) {
				drawalpha(&imgH1R, x1 - 28, y1 - 20);
			}
		}
		//左向角色动图
		if (force == 1) {
			if (t % 4 == 0 && t % 2 == 0) {
				drawalpha(&imgW1L, x1 - 10, y1 - 23);
			}
			else if (t % 2 == 1) {
				drawalpha(&imgW1, x1 - 10, y1 - 23);
			}
			else if (t % 4 == 2 && t % 2 == 0) {
				drawalpha(&imgW1R, x1 - 10, y1 - 23);
			}
		}
		swprintf_s(filename, L"按下S键进入室内");
		outtextxy(1650, 200, filename);
		FlushBatchDraw();
		Sleep(150);
		t++;
		i++;
		j1++;
		j2++;
		j3++;
		j4++;
		j5++;
	}
}
void room(ELE* character) {
	int t = 0, i = 0, j1 = 0, j2 = 0, value, xmid = 960, dertaD, lp, dertaY, ymid = 540, force = 0, dertax = 50, dertay = 50, xnow = 960, ynow = 540, x1 = 800, y1 = 600, dx = 0, dy = 0, tick1 = 0;
	ExMessage msg;
	wchar_t filename[100];
	MAP* q;
	IMAGE imgW1L, imgW1R, imgW1, imgH1L, imgH1R, imgH1, window1,window2,lightcao[2],tree,wei,bed,carpet,lamp,pot,burner,qiumask,qiu,pingfeng;
	loadimage(&imgW1R, L"./LRIGHT.png", 196, 294);
	loadimage(&imgW1L, L"./LLEFT.png", 202, 294);
	loadimage(&imgW1, L"./LNORMAL.png", 196, 296);
	loadimage(&imgH1R, L"./RRIGHT.png", 202, 294);
	loadimage(&imgH1L, L"./RLEFT.png", 192, 300);
	loadimage(&imgH1, L"./RNORMAL.png", 198, 296);
	IMAGE room;
	loadimage(&room, L"室内.png", 5000, 1080);
	loadimage(&window1, L"窗户.png", 218, 312);
	loadimage(&window2, L"窗户.png", 218, 312);
	loadimage(&lightcao[0], L"小灯草0.png", 147, 184);
	loadimage(&lightcao[1], L"小灯草1.png", 147, 184);
	loadimage(&tree, L"爆炎树.png", 288, 288);
	loadimage(&wei, L"大伟.png", 288, 288);
	loadimage(&bed, L"床.png", 288, 288);
	loadimage(&carpet, L"地毯.png", 288, 288);
	loadimage(&lamp, L"灯笼.png", 288, 288);
	loadimage(&pot, L"锅.png", 288, 288);
	loadimage(&burner, L"火炉.png", 288, 288);
	loadimage(&qiumask, L"面具壁挂.png", 288, 288);
	loadimage(&qiu, L"丘丘壁挂.png", 288, 288);
	loadimage(&pingfeng, L"屏风.png", 288, 288);
	while (1) {
		if (GetAsyncKeyState(27)) {
			break;
		}
		if (GetAsyncKeyState(90)) {
			setpoint(character, xnow, ynow);
		}
		cleardevice();
		BeginBatchDraw();
		if (xnow > 940 && xnow < 940+2*218 &&ynow < 700) {
			swprintf_s(filename, L"按下F键以交互");
			outtextxy(1650, 300, filename);
			if (GetAsyncKeyState(70)) {
				if (tick1 == 0) {
					tick1 = 1;
				}
				else {
					tick1 = 0;
				}
			}
		}
		backpack(character);
		drawAlphaplus(&room, 0, 0, 1920, 1080, xnow - 960, ynow - 540,1);
		if (tick1 == 0) {
			drawAlphaplus(&window1, 735 - xnow + 960, 190 - ynow + 540, 218, 312, 0, 0, 1);
			drawAlphaplus(&window2, 953 - xnow + 960, 190 - ynow + 540, 218, 312, 0, 0, 1);
		}
		q = character->headroom;
		while (q != NULL) {
			if (q->lei == 0) {
				drawAlphaplus(&lightcao[0], q->x - xnow + 960, q->y - ynow + 540, 147, 184, 0, 0, 1);
			}
			if (q->lei == 1) {
				drawAlphaplus(&tree, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			if (q->lei == 2) {
				drawAlphaplus(&wei, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			if (q->lei == 3) {
				drawAlphaplus(&bed, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			if (q->lei == 4) {
				drawAlphaplus(&carpet, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			if (q->lei == 5) {
				drawAlphaplus(&lamp, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			if (q->lei == 6) {
				drawAlphaplus(&pot, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			if (q->lei == 7) {
				drawAlphaplus(&burner, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			if (q->lei == 8) {
				drawAlphaplus(&qiumask, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			if (q->lei == 9) {
				drawAlphaplus(&qiu, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			if (q->lei == 10) {
				drawAlphaplus(&pingfeng, q->x - xnow + 960, q->y - ynow + 540, 288, 288, 0, 0, 1);
			}
			q = q->next;
		}
		lp = _kbhit();
		if (lp != 0) {
			dertax = 50;
			dertay = 40;
			if (GetAsyncKeyState(VK_CONTROL)) {
				dertax = dertax + 20;
				dertay = dertay + 16;
			}
			if (GetAsyncKeyState(VK_RIGHT)) {
				dertaD = xmid - x1;
				printf("%d", dertaD);
				if (dertaD > -400) {
					x1 += dertax;
					dx += dertax;
				}
				else {
					if (xnow < 3460) {
						xnow += dertax / 2;
					}
				}
				force = 0;
			}
			if (GetAsyncKeyState(VK_LEFT)) {
				dertaD = xmid - x1;
				printf("%d", dertaD);
				if (dertaD < 500) {
					x1 -= dertax;
					dx -= dertax;
				}
				else {
					if (xnow > 960) {
						xnow -= dertax / 2;
					}
				}
				force = 1;
			}
			if (GetAsyncKeyState(VK_DOWN)) {
				dertaY = ymid - y1;
				printf("%d", dertaY);
				if (dertaY > -160) {
					y1 += dertay;
					dy += dertay;
				}
				else {

				}
			}
			if (GetAsyncKeyState(VK_UP)) {
				dertaY = ymid - y1;
				printf("%d", dertaY);
				if (dertaY < 80) {
					y1 -= dertay;
					dy -= dertay;
				}
				else {

				}
			}
		}
		//右向角色动图
		if (force == 0) {
			if (t % 4 == 0 && t % 2 == 0) {
				drawalpha(&imgH1L, x1 - 28, y1 - 23);
			}
			else if (t % 2 == 1) {
				drawalpha(&imgH1, x1 - 28, y1 - 20);
			}
			else if (t % 4 == 2 && t % 2 == 0) {
				drawalpha(&imgH1R, x1 - 28, y1 - 20);
			}
		}
		//左向角色动图
		if (force == 1) {
			if (t % 4 == 0 && t % 2 == 0) {
				drawalpha(&imgW1L, x1 - 10, y1 - 23);
			}
			else if (t % 2 == 1) {
				drawalpha(&imgW1, x1 - 10, y1 - 23);
			}
			else if (t % 4 == 2 && t % 2 == 0) {
				drawalpha(&imgW1R, x1 - 10, y1 - 23);
			}
		}
		swprintf_s(filename, L"按下Z键以进入摆设放置界面");
		outtextxy(1500, 200, filename);
		FlushBatchDraw();
		Sleep(150);
		t++;
	}
}
void setpoint(ELE* character,int xnow,int ynow) {
	IMAGE lightcao[2],tree, wei, bed, carpet, lamp, pot, burner, qiumask, qiu, pingfeng,tree1,wei1,bed1,carpet1,lamp1,pot1,burner1,qiumask1,qiu1,pingfeng1;
	MAP* p=NULL, *q=NULL, *h=NULL,*t=NULL,*s=NULL;
	loadimage(&lightcao[0], L"小灯草0.png", 147, 184);
	loadimage(&lightcao[1], L"小灯草1.png", 147, 184);
	loadimage(&tree, L"爆炎树.png", 288, 288);
	loadimage(&wei, L"大伟.png", 288, 288);
	loadimage(&bed, L"床.png", 288, 288);
	loadimage(&carpet, L"地毯.png", 288, 288);
	loadimage(&lamp, L"灯笼.png", 288, 288);
	loadimage(&pot, L"锅.png", 288, 288);
	loadimage(&burner, L"火炉.png", 288, 288);
	loadimage(&qiumask, L"面具壁挂.png", 288, 288);
	loadimage(&qiu, L"丘丘壁挂.png", 288, 288);
	loadimage(&pingfeng, L"屏风.png", 288, 288);
	loadimage(&tree1, L"爆炎树.png", 144, 144);
	loadimage(&wei1, L"大伟.png", 144, 144);
	loadimage(&bed1, L"床.png", 144, 144);
	loadimage(&carpet1, L"地毯.png", 144, 144);
	loadimage(&lamp1, L"灯笼.png", 144, 144);
	loadimage(&pot1, L"锅.png", 144, 144);
	loadimage(&burner1, L"火炉.png", 144, 144);
	loadimage(&qiumask1, L"面具壁挂.png", 144, 144);
	loadimage(&qiu1, L"丘丘壁挂.png", 144, 144);
	loadimage(&pingfeng1, L"屏风.png", 144, 144);
	drawAlphaplus(&lightcao[0], 100, 800,147,184,0,0,1);
	drawAlphaplus(&tree1, 250, 800, 144, 144, 0, 0, 1);
	drawAlphaplus(&wei1, 400, 800, 144, 144, 0, 0, 1);
	drawAlphaplus(&bed1, 550, 800, 144, 144, 0, 0, 1);
	drawAlphaplus(&carpet1, 700, 800, 144, 144, 0, 0, 1);
	drawAlphaplus(&lamp1, 850, 800, 144, 144, 0, 0, 1);
	drawAlphaplus(&pot1, 1000, 800, 144, 144, 0, 0, 1);
	drawAlphaplus(&burner1, 1150, 800, 144, 144, 0, 0, 1);
	drawAlphaplus(&qiumask1, 1300, 800, 144, 144, 0, 0, 1);
	drawAlphaplus(&qiu1, 1450, 800, 144, 144, 0, 0, 1);
	drawAlphaplus(&pingfeng1, 1600, 800, 144, 144, 0, 0, 1);
	outtextxy(20, 170, L"在家具处按下鼠标左键以选择");
	FlushBatchDraw();
	while (1) {
		if (MouseHit())
		{
			MOUSEMSG msg = GetMouseMsg();//消息分发
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 100 && msg.x < 249 && msg.y> 800 && msg.y < 1000){
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawalpha(&lightcao[1], 100, 800);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei==0) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y < 400) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&lightcao[0], msg.x, msg.y, 147, 184, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 0;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 250 && msg.x < 399 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&tree1, 250, 600, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei==1) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y < 400) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&tree,msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 1;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 400 && msg.x < 549 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&wei1, 400, 600, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei == 2) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y > 700) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&wei, msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 2;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 550 && msg.x < 699 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&bed1, 550, 600, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei == 3) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y < 400) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&bed, msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 3;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 700 && msg.x < 849 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&carpet1, 700 - xnow + 960, 600 - ynow + 540, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei == 4) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y < 400) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&carpet, msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 4;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 850 && msg.x < 999 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&lamp1, 850 - xnow + 960, 600 - ynow + 540, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei == 5) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y > 700) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&lamp, msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 5;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 1000 && msg.x < 1149 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&pot1, 1000 - xnow + 960, 600 - ynow + 540, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei == 6) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y < 400) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&pot, msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 6;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 1150 && msg.x < 1299 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&burner1, 1150 - xnow + 960, 600 - ynow + 540, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei == 7) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y < 400) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&burner, msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 7;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 1300 && msg.x < 1449 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&qiumask1, 1300 - xnow + 960, 600 - ynow + 540, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei == 8) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y > 700) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&qiumask, msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 8;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 1450 && msg.x < 1599 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&qiu1, 1450 - xnow + 960, 600 - ynow + 540, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei == 9) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y > 700) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&qiu, msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 9;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
			if (msg.uMsg == WM_LBUTTONDOWN && msg.x > 1600 && msg.x < 1749 && msg.y> 800 && msg.y < 1000) {
				printf("坐标(%d,%d)\n", msg.x, msg.y);
				BeginBatchDraw();
				drawAlphaplus(&pingfeng1, 1600 - xnow + 960, 600 - ynow + 540, 144, 144, 0, 0, 0.6);
				outtextxy(20, 20, L"点击后放置在点击处");
				outtextxy(20, 70, L"ctrl点击后删除点击处的家具");
				FlushBatchDraw();
				flushmessage();
				while (1) {
					if (MouseHit()) {
						MOUSEMSG msg = GetMouseMsg();
						if (msg.uMsg == WM_LBUTTONDOWN) {
							if (GetAsyncKeyState(VK_CONTROL)) {
								t = character->headroom;
								while (t != NULL) {
									if (msg.x > t->x - 10 && msg.x<t->x + 90 && msg.y>t->y - 10 && msg.y < t->y + 90 && t->lei == 10) {
										if (t == character->headroom) {
											character->headroom = t->next;
											s = t;
											t = t->next;
											free(s);
										}
										else if (t->next == NULL) {
											t = NULL;
											s->next = NULL;
											break;
										}
										else {
											s->next = t->next;
											free(t);
											t = s->next;
										}
									}
									else {
										s = t;
										t = t->next;
									}
								}
								break;
							}
							else {
								if (msg.y < 400) {
									BeginBatchDraw();
									outtextxy(20, 120, L"不可放置在此处");
									FlushBatchDraw();
									Sleep(1000);
									break;
								}
								else {
									BeginBatchDraw();
									drawAlphaplus(&pingfeng, msg.x, msg.y, 288, 288, 0, 0, 1);
									outtextxy(20, 120, L"放置成功");
									FlushBatchDraw();
									Sleep(1000);
									p = (MAP*)malloc(sizeof(MAP));
									p->x = msg.x + xnow - 960;
									p->y = msg.y + ynow - 540;
									p->lei = 10;
									p->next = NULL;
									h = character->headroom;
									if (h == NULL) {
										character->headroom = p;
									}
									else {
										while (h != NULL) {
											q = h;
											h = h->next;
										}
										q->next = p;
									}
									break;
								}
							}
						}
					}
				}
				break;
			}
		}
		if (GetAsyncKeyState(27)) {
			BeginBatchDraw();
			break;
		}
	}
}
ELE* enemyset0() {
	int j, life[3] = { 10,10,10 }, i;
	ELE* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 3; j++) {
		p = (ELE*)malloc(sizeof(ELE));
		p->cm = 1;
		p->life = life[j];
		p->finallife = life[j];
		p->level = 10;
		p->elenow1 = 0;
		p->elenow2 = 0;
		p->freeze = 0;
		p->enemy = j;
		p->attele = 0;
		p->norshield = 0;
		p->difshield = 0;
		p->immune = -1;
		p->eleshield = 0;
		p->active = 0;
		p->fire = 0;
		p->attend = 0;
		p->grasscore = 0;
		p->monster = j;
		for (i = 0; i < 6; i++) {
			p->furniture[i] = 0;
		}
		if (j == 0) {
			p->att = 2;
		}
		else {
			p->att = 1;
		}
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
ELE* enemyset1() {
	int j, life[3] = { 15,10,10 }, i;
	ELE* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 3; j++) {
		p = (ELE*)malloc(sizeof(ELE));
		p->cm = 1;
		p->life = life[j];
		p->finallife = life[j];
		p->level = 20;
		p->elenow2 = 0;
		p->freeze = 0;
		p->enemy = j;//怪物次序
		p->norshield = 0;
		p->difshield = 0;
		p->active = 0;
		p->fire = 0;
		p->attend = 0;
		p->grasscore = 0;
		p->immune = -1;
		for (i = 0; i < 6; i++) {
			p->furniture[i] = 0;
		}
		if (j == 0) {
			p->att = 2;
			p->attele = 2;
			p->eleshield = 2;
			p->elenow1 = 2;
			p->monster = 3;//怪物类型
		}
		else if (j == 1) {
			p->att = 1;
			p->attele = 0;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->monster = 6;//怪物类型
		}
		else {
			p->att = 1;
			p->attele = 0;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->monster = 6;//怪物类型
		}
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
ELE* enemyset2() {
	int j, life[3] = { 15,10,10 }, i;
	ELE* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 3; j++) {
		p = (ELE*)malloc(sizeof(ELE));
		p->cm = 1;
		p->life = life[j];
		p->finallife = life[j];
		p->level = 30;
		p->elenow2 = 0;
		p->freeze = 0;
		p->enemy = j;//怪物次序
		p->norshield = 0;
		p->difshield = 0;
		p->active = 0;
		p->fire = 0;
		p->attend = 0;
		p->grasscore = 0;
		p->immune = -1;
		for (i = 0; i < 6; i++) {
			p->furniture[i] = 0;
		}
		if (j == 0) {
			p->att = 2;
			p->attele = 2;
			p->eleshield = 2;
			p->elenow1 = 2;
			p->monster = 3;//怪物类型
		}
		else if (j == 1) {
			p->att = 1;
			p->attele = 7;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->monster = 5;//怪物类型
		}
		else {
			p->att = 1;
			p->attele = 7;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->monster = 5;//怪物类型
		}
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
ELE* enemyset3() {
	int j, life[3] = { 15,15,15 }, i;
	ELE* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 3; j++) {
		p = (ELE*)malloc(sizeof(ELE));
		p->cm = 1;
		p->life = life[j];
		p->finallife = life[j];
		p->level = 40;
		p->elenow2 = 0;
		p->freeze = 0;
		p->enemy = j;//怪物次序
		p->norshield = 0;
		p->difshield = 0;
		p->active = 0;
		p->fire = 0;
		p->attend = 0;
		p->grasscore = 0;
		p->immune = -1;
		for (i = 0; i < 6; i++) {
			p->furniture[i] = 0;
		}
		if (j == 0) {
			p->att = 2;
			p->attele = 2;
			p->eleshield = 2;
			p->elenow1 = 2;
			p->monster = 3;//怪物类型
		}
		else if (j == 1) {
			p->att = 1;
			p->attele = 1;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->monster = 1;//怪物类型
		}
		else {
			p->att = 1;
			p->attele = 3;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->monster = 2;//怪物类型
		}
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
ELE* enemyset4() {
	int j, life[3] = { 15,10,10 }, i;
	ELE* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 3; j++) {
		p = (ELE*)malloc(sizeof(ELE));
		p->cm = 1;
		p->life = life[j];
		p->finallife = life[j];
		p->level = 50;
		p->elenow2 = 0;
		p->freeze = 0;
		p->enemy = j;//怪物次序
		p->norshield = 0;
		p->difshield = 0;
		p->active = 0;
		p->fire = 0;
		p->attend = 0;
		p->grasscore = 0;
		for (i = 0; i < 6; i++) {
			p->furniture[i] = 0;
		}
		if (j == 0) {
			p->att = 2;
			p->attele = 1;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->immune = -1;
			p->monster = 1;//怪物类型
		}
		else if (j == 1) {
			p->att = 1;
			p->attele = 5;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->immune = 5;
			p->monster = 4;//怪物类型
		}
		else {
			p->att = 1;
			p->attele = 5;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->immune = 5;
			p->monster = 4;//怪物类型
		}
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
ELE* enemyset5() {
	int j, life[3] = { 15,10,15 }, i;
	ELE* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 3; j++) {
		p = (ELE*)malloc(sizeof(ELE));
		p->cm = 1;
		p->life = life[j];
		p->finallife = life[j];
		p->level = 60;
		p->elenow2 = 0;
		p->freeze = 0;
		p->enemy = j;//怪物次序
		p->difshield = 0;
		p->active = 0;
		p->fire = 0;
		p->attend = 0;
		p->grasscore = 0;
		p->immune = -1;
		for (i = 0; i < 6; i++) {
			p->furniture[i] = 0;
		}
		if (j == 0) {
			p->att = 1;
			p->attele = 1;
			p->eleshield = 0;
			p->norshield = 2;
			p->elenow1 = 0;
			p->monster = 1;//怪物类型
		}
		else if (j == 1) {
			p->att = 2;
			p->attele = 0;
			p->eleshield = 0;
			p->norshield = 0;
			p->elenow1 = 0;
			p->monster = 6;//怪物类型
		}
		else {
			p->att = 1;
			p->attele = 3;
			p->eleshield = 0;
			p->norshield = 2;
			p->elenow1 = 0;
			p->monster = 2;//怪物类型
		}
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
ELE* enemyset6() {
	int j, life[1] = { 40 }, i;
	ELE* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 1; j++) {
		p = (ELE*)malloc(sizeof(ELE));
		p->cm = 1;
		p->life = life[j];
		p->finallife = life[j];
		p->level = 70;
		p->elenow2 = 0;
		p->freeze = -100;
		p->enemy = j;//怪物次序
		p->norshield = 0;
		p->difshield = 0;
		p->active = 0;
		p->fire = 999;
		p->attend = 0;
		p->grasscore = 0;
		p->immune = -1;
		for (i = 0; i < 6; i++) {
			p->furniture[i] = 0;
		}
		if (j == 0) {
			p->att = 4;
			p->attele = 2;
			p->eleshield = 0;
			p->elenow1 = 0;
			p->monster = 7;//怪物类型
		}
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
ELE* enemyset7() {
	int j, life[3] = { 40 }, i;
	ELE* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 1; j++) {
		p = (ELE*)malloc(sizeof(ELE));
		p->cm = 1;
		p->life = life[j];
		p->finallife = life[j];
		p->level = 80;
		p->elenow2 = 0;
		p->freeze = -100;
		p->enemy = j;//怪物次序
		p->norshield = 0;
		p->difshield = 0;
		p->active = 0;
		p->fire = 0;
		p->attend = 0;
		p->grasscore = 0;
		p->immune = -1;
		for (i = 0; i < 6; i++) {
			p->furniture[i] = 0;
		}
		if (j == 0) {
			p->att = 4;
			p->attele = 2;
			p->eleshield = 4;
			p->elenow1 = 2;
			p->monster = 8;//怪物类型
			p->norshield = 0;
		}
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
ELE* enemyset8() {
	int j, life[3] = { 15,15,15 }, i;
	ELE* head = NULL, * p, * h, * q = NULL;//输入物品
	for (j = 0; j < 3; j++) {
		p = (ELE*)malloc(sizeof(ELE));
		p->cm = 1;
		p->life = life[j];
		p->finallife = life[j];
		p->level = 90;
		p->elenow2 = 0;
		p->freeze = 0;
		p->enemy = j;//怪物次序
		p->norshield = 0;
		p->active = 0;
		p->fire = 0;
		p->attend = 0;
		p->grasscore = 0;
		p->immune = -1;
		for (i = 0; i < 6; i++) {
			p->furniture[i] = 0;
		}
		if (j == 0) {
			p->att = 2;
			p->attele = 2;
			p->eleshield = 2;
			p->difshield = 0;
			p->elenow1 = 2;
			p->monster = 3;//怪物类型
		}
		else if (j == 1) {
			p->att = 1;
			p->attele = 1;
			p->eleshield = 0;
			p->difshield = 2;
			p->elenow1 = 0;
			p->monster = 1;//怪物类型
		}
		else {
			p->att = 1;
			p->attele = 3;
			p->eleshield = 0;
			p->difshield = 2;
			p->elenow1 = 0;
			p->monster = 2;//怪物类型
		}
		p->next = NULL;
		h = head;
		if (h == NULL) {
			head = p;
		}
		else {
			while (h != NULL) {
				q = h;
				h = h->next;
			}
			q->next = p;
		}
	}
	return head;
}
void juqing() {
	BeginBatchDraw();
	wchar_t filename[100];
	swprintf_s(filename, L"你是穿越世界诸多星海的一名旅者，你来到了一座尘封已久的图书馆");
	outtextxy(700, 100, filename);
	FlushBatchDraw();
	Sleep(2000);
	BeginBatchDraw();
	swprintf_s(filename, L"在图书馆的某处你打开了一本布满灰尘的书");
	outtextxy(700, 150, filename);
	FlushBatchDraw();
	Sleep(2000);
	BeginBatchDraw();
	swprintf_s(filename, L"打开书的瞬间，灰尘散去，一缕光芒从书中射出");
	outtextxy(700, 200, filename);
	FlushBatchDraw();
	Sleep(2000);
	BeginBatchDraw();
	swprintf_s(filename, L"回过神来你已经来到了一座荒岛上，这里荒无人烟");
	outtextxy(700, 250, filename);
	FlushBatchDraw();
	Sleep(2000);
	BeginBatchDraw();
	swprintf_s(filename, L"不幸的是，你的力量已然流失，这座岛屿上似乎存在着不少强大的怪物");
	outtextxy(700, 300, filename);
	FlushBatchDraw();
	Sleep(2000);
	BeginBatchDraw();
	swprintf_s(filename, L"你能感觉到，在他们身上存在着某种未知的力量");
	outtextxy(700, 350, filename);
	FlushBatchDraw();
	Sleep(2000);
	BeginBatchDraw();
	swprintf_s(filename, L"击败他们，并控制这座岛屿吧！");
	outtextxy(700, 400, filename);
	FlushBatchDraw();
	Sleep(2000);
	BeginBatchDraw();
	swprintf_s(filename, L"按下左键以继续");
	outtextxy(700, 450, filename);
	FlushBatchDraw();
	while (1) {
		if (MouseHit()) {
			MOUSEMSG msg = GetMouseMsg();
			if (msg.uMsg == WM_LBUTTONDOWN) {
				break;
			}
		}
	}
	BeginBatchDraw();
	swprintf_s(filename, L"按下up,down,right,left以移动");
	outtextxy(700, 600, filename);
	swprintf_s(filename, L"按下ctrl以加速");
	outtextxy(700, 650, filename);
	swprintf_s(filename, L"按下F与物品交互");
	outtextxy(700, 700, filename);
	swprintf_s(filename, L"角色生命值不足时可按下T使用生命药水,按下E食用日落果");
	outtextxy(700, 750, filename);
	swprintf_s(filename, L"按下B以打开背包");
	outtextxy(700, 800, filename);
	swprintf_s(filename, L"M为音游小游戏");
	outtextxy(700, 850, filename);
	swprintf_s(filename, L"获得梦境之力后P键进入家园");
	outtextxy(700, 900, filename);
	swprintf_s(filename, L"家园中按下S键以进入室内");
	outtextxy(700, 950, filename);
	swprintf_s(filename, L"按下左键以继续");
	outtextxy(700, 1000, filename);
	FlushBatchDraw();
	while (1) {
		if (MouseHit()) {
			MOUSEMSG msg = GetMouseMsg();
			if (msg.uMsg == WM_LBUTTONDOWN) {
				PlaySound(NULL, NULL, SND_PURGE);
				break;
			}
		}
	}
}
void battlejuqing() {
	IMAGE d[10];
	loadimage(&d[0], L"烟花背景0.png", 1920, 1080);
	POINT pts[] = {{400, 940}, {1330, 940},{1330,1020},{400,1020} };
	polygon(pts, 4);
	POINT pts1[] = {{300, 50}, {1370, 50},{1370,120},{300,120} };
	polygon(pts1, 4);
	POINT pts2[] = { {150, 300}, {1000, 300},{1000,850},{150,850} };
	polygon(pts2, 4);
	POINT pts3[] = { {1500, 80}, {1900, 80},{1900,250},{1500,250} };
	polygon(pts3, 4);
	POINT pts4[] = { {1700, 300}, {1900, 300},{1900,500},{1700,500} };
	polygon(pts4, 4);
	POINT pts5[] = { {1200, 300}, {1300, 300},{1300,400},{1200,400} };
	polygon(pts5, 4);
	wchar_t filename[100];
	swprintf_s(filename, L"此处显示角色等级及血量");
	outtextxy(500, 900, filename);
	swprintf_s(filename, L"此处显示提示信息");
	outtextxy(1500, 30, filename);
	swprintf_s(filename, L"此处显示发生的元素反应信息");
	outtextxy(1150, 410, filename);
	swprintf_s(filename, L"此处显示双方伤害信息");
	outtextxy(1550, 260, filename);
	swprintf_s(filename, L"此处显示怪物等级及血量，当前出战怪物血条下方有皇冠提示，只有它才能受到角色的直接攻击");
	outtextxy(250, 130, filename);
	swprintf_s(filename, L"此处显示怪物模型，从左到右，从上到下依次对应上方三个血条，模型右上方显示剩余护盾量");
	outtextxy(100, 250, filename);
	settextcolor(YELLOW);
	settextstyle(40, 20, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
	swprintf_s(filename, L"按下SPACE（空格）：我知道了");
	outtextxy(1300, 900, filename);
	FlushBatchDraw();
	while (1) {
		if (GetAsyncKeyState(32)) {
			break;
		}
	}
	BeginBatchDraw();
	cleardevice();
	drawAlphaplus0(&d[0], 0, 0, 0, 0, 1);
	swprintf_s(filename, L"一.战斗机制");
	outtextxy(100, 100, filename);
	swprintf_s(filename, L"二.角色技能");
	outtextxy(800, 100, filename);
	swprintf_s(filename, L"按下ENTER：我知道了");
	outtextxy(1300, 900, filename);
	settextcolor(WHITE);
	settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
	swprintf_s(filename, L"  1.角色血量继承自大世界血量，所以在开始");
	outtextxy(100, 200, filename);
	swprintf_s(filename, L"  战斗前，请务必保证角色状态良好");
	outtextxy(100, 250, filename);
	swprintf_s(filename, L"  2.角色初始仅能使用物理攻击，只有在地图");
	outtextxy(100, 300, filename);
	swprintf_s(filename, L"  上与精灵生物互动后才可使用元素技能");
	outtextxy(100, 350, filename);
	swprintf_s(filename, L"  3.角色使用元素技能，或敌人使用时均能使");
	outtextxy(100, 400, filename);
	swprintf_s(filename, L"  对方身上附着对应的元素状态，如果对方身");
	outtextxy(100, 450, filename);
	swprintf_s(filename, L"  上已有元素附着，将会触发元素反应，造成"); 
	outtextxy(100, 500, filename);
	swprintf_s(filename, L"  额外伤害和效果，起到增益效果");
	outtextxy(100, 550, filename);
	swprintf_s(filename, L"  4.角色在每一回合开始时拥有8点行动点，");
	outtextxy(100, 600, filename);
	swprintf_s(filename, L"  在使用元素攻击时消耗3点行动点，物理攻");
	outtextxy(100, 650, filename);
	swprintf_s(filename, L"  击消耗两点，若行动点清空，则该回合结束");
	outtextxy(100, 700, filename);
	swprintf_s(filename, L"  此时将发动延迟攻击，并在此后怪物反击");
	outtextxy(100, 750, filename);
	swprintf_s(filename, L"  ，死亡的怪物不会发动攻击，伤害结算后"); 
	outtextxy(100, 800, filename);
	swprintf_s(filename, L"  进入下一回合，行动点重置。");
	outtextxy(100, 850, filename);
	swprintf_s(filename, L"  5.当双方有一方血量全部清空时，战斗结");
	outtextxy(100, 900, filename);
	swprintf_s(filename, L"  束，并根据剩余血量结算成绩。");
	outtextxy(100, 950, filename);
	swprintf_s(filename, L"  物理攻击：对敌方单位造成两点物理伤害");
	outtextxy(800, 200, filename);
	swprintf_s(filename, L"  水元素攻击：对敌方单位造成一点水元素伤害");
	outtextxy(800, 250, filename);
	swprintf_s(filename, L"   ，并恢复两点生命值");
	outtextxy(800, 300, filename);
	swprintf_s(filename, L"  火元素攻击：对敌方单位造成两点火元素伤害");
	outtextxy(800, 350, filename);
	swprintf_s(filename, L"  冰元素攻击：对敌方单位造成一点冰元素伤害"); 
	outtextxy(800, 400, filename);
	swprintf_s(filename, L"  ，并产生三点次数盾（单次格挡一点伤害）");
	outtextxy(800, 450, filename);
	swprintf_s(filename, L"  雷元素攻击：对敌方单位造成一点雷元素伤害");
	outtextxy(800, 500, filename);
	swprintf_s(filename, L"  ，并产生两次延迟攻击（每回合结束一次）");
	outtextxy(800, 550, filename);
	swprintf_s(filename, L"  风元素攻击：对敌方单位造成两点风元素伤害");
	outtextxy(800, 600, filename);
	swprintf_s(filename, L"  岩元素攻击：对敌方单位造成两点岩元素伤害");
	outtextxy(800, 650, filename);
	swprintf_s(filename, L"  草元素攻击：对敌方单位造成两点草元素伤害");
	outtextxy(800, 700, filename);
	FlushBatchDraw();
	while (1) {
		if (GetAsyncKeyState(13)) {
			break;
		}
	}
	BeginBatchDraw();
	cleardevice();
	drawAlphaplus0(&d[0], 0, 0, 0, 0, 1);
	settextcolor(YELLOW);
	settextstyle(40, 20, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
	swprintf_s(filename, L"三.元素反应");
	outtextxy(600, 100, filename);
	swprintf_s(filename, L"按下ALT：我懂完了");
	outtextxy(100, 900, filename);
	swprintf_s(filename, L"按下CTRL：啊对对对");
	outtextxy(100, 800, filename);
	settextcolor(WHITE);
	settextstyle(30, 15, _T("Consolas"), 0, 0, 1000, FALSE, FALSE, FALSE);
	swprintf_s(filename, L"  不同的元素在相遇时会发生各异的元素反应");
	outtextxy(600, 150, filename);
	swprintf_s(filename, L"  1.水+火：蒸发反应（本次攻击伤害＋2）");
	outtextxy(600, 200, filename);
	swprintf_s(filename, L"  2.水+冰：冻结反应（本次攻击伤害＋1，敌人在本回合停止行动）");
	outtextxy(600, 250, filename);
	swprintf_s(filename, L"  3.水+雷：感电反应（本次攻击伤害＋1，对所有后方单位产生一点穿透伤害）");
	outtextxy(600, 300, filename);
	swprintf_s(filename, L"  4.水+草：绽放反应（本次攻击伤害＋1，产生草原核）");
	outtextxy(600, 350, filename);
	swprintf_s(filename, L"  5.火+冰：融化反应（本次攻击伤害＋2）");
	outtextxy(600, 400, filename);
	swprintf_s(filename, L"  6.火+雷：超载反应（本次攻击伤害＋1，对方切换至下一角色）");
	outtextxy(600, 450, filename);
	swprintf_s(filename, L"  7.火+草：燃烧反应（本次攻击伤害＋1，产生两次火元素延迟攻击）");
	outtextxy(600, 500, filename);
	swprintf_s(filename, L"  8.冰+雷：超导反应（本次攻击伤害＋1，对所有后方单位产生一点穿透伤害）");
	outtextxy(600, 550, filename);
	swprintf_s(filename, L"  9.雷+草：原激化反应（本次攻击伤害＋1，产生2次激化领域，该单位下次");
	outtextxy(600, 600, filename);
	swprintf_s(filename, L"  进行草元素或雷元素攻击时分别产生蔓激化，超激化反应并使伤害+1）");
	outtextxy(600, 650, filename);
	swprintf_s(filename, L"  10.风+（水火冰雷之一）：扩散反应（本次攻击伤害＋1，将目标身上元素扩");
	outtextxy(600, 700, filename);
	swprintf_s(filename, L"  散到其他敌方单位身上，并触发元素反应）");
	outtextxy(600, 750, filename);
	swprintf_s(filename, L"  11.岩+（水火冰雷之一）：结晶反应（产生两点护盾）");
	outtextxy(600, 800, filename);
	swprintf_s(filename, L"  12.草原核+火元素：烈绽放反应（本次攻击伤害＋2）");
	outtextxy(600, 850, filename);
	swprintf_s(filename, L"  12.草原核+雷元素：超绽放反应（本次攻击伤害＋2）");
	outtextxy(600, 900, filename);
	swprintf_s(filename, L"  附：草，冰不反应，岩与风不产生元素附着，元素反应后无对应元素残留");
	outtextxy(600, 950, filename);
	FlushBatchDraw();
	while (1) {
		if (GetAsyncKeyState(18)) {
			break;
		}
		if (GetAsyncKeyState(17)) {
			break;
		}
	}
}
