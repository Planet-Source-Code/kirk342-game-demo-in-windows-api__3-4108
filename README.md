<div align="center">

## Game demo in Windows API


</div>

### Description

This code demonstrates simple graphics that can be done in Win32 API. Just make a new Win32 application, paste the code there, compile it, and run the program.
 
### More Info
 


<span>             |<span>
---                |---
**Submitted On**   |
**By**             |[Kirk342](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByAuthor/kirk342.md)
**Level**          |Beginner
**User Rating**    |4.6 (41 globes from 9 users)
**Compatibility**  |C, C\+\+ \(general\), Microsoft Visual C\+\+
**Category**       |[Windows CE](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByCategory/windows-ce__3-30.md)
**World**          |[C / C\+\+](https://github.com/Planet-Source-Code/PSCIndex/blob/master/ByWorld/c-c.md)
**Archive File**   |[](https://github.com/Planet-Source-Code/kirk342-game-demo-in-windows-api__3-4108/archive/master.zip)

### API Declarations

```
#include <windows.h>
#include <windowsx.h>
#include <stdlib.h>
#include <stdio.h>
```


### Source Code

```
#define WIN32_LEAN_AND_MEAN
#include <windows.h>
#include <windowsx.h>
#include <stdlib.h>
#include <stdio.h>
const char g_szClassName[] = "Demo";
#define KEY_DOWN(vk_code) ((GetAsyncKeyState(vk_code) & 0x8000) ? 1 : 0)
// window info
#define WINDOW_WIDTH 640
#define WINDOW_HEIGHT 480
//globals
HDC hdc;
HWND global_hwnd;
HPEN blue_pen, black_pen;
HBRUSH green_brush, red_brush, black_brush, old_brush;
struct Ball
{
	int velocity_x;
	int velocity_y;
	int position_x;
	int position_y;
	int radius;
	RECT rect;
} ball;
struct Paddle
{
	int height;
	int width;
	int upper_x_position;
	int upper_y_position;
	int bottom_x_position;
	int bottom_y_position;
	int paddle_velocity;
} paddle;
// used for score() function
char buffer[80];
int score =0;
//functions
void initialize_ball(void);
void draw_ball(void);
void draw_score(void);
void initialize_paddle(void);
void draw_paddle(void);
LRESULT CALLBACK WndProc(HWND hwnd,
						 UINT msg,
						 WPARAM wparam,
						 LPARAM lparam)
{
	switch(msg)
		{
		case WM_CREATE:
    {
			//create pens and brushes
			blue_pen = CreatePen(PS_SOLID,0, RGB(0,0,255));
			black_pen = CreatePen(PS_SOLID,0, RGB(0,0,0));
			green_brush = CreateSolidBrush(RGB(0, 255, 0));
			red_brush = CreateSolidBrush(RGB(255, 0, 0));
			black_brush = CreateSolidBrush(RGB(0, 0, 0));
			return(0);
		} break;
		case WM_DESTROY:
		{
			//delete pens and brushes
			DeleteObject(blue_pen);
			DeleteObject(black_pen);
			DeleteObject(green_brush);
			DeleteObject(red_brush);
			DeleteObject(black_brush);
			PostQuitMessage(0);
			return(0);
		} break;
	default:break;
  }
return (DefWindowProc(hwnd, msg, wparam, lparam));
}
int WINAPI WinMain(	HINSTANCE hinstance,
				  HINSTANCE hprevinstance,
				  LPSTR lpcmdline, int ncmdshow)
{
	WNDCLASSEX sd;
	MSG msg;
	HWND hwnd;
	sd.cbSize    = sizeof(WNDCLASSEX);
	sd.style			= CS_HREDRAW | CS_VREDRAW;
	sd.lpfnWndProc	= WndProc;
	sd.cbClsExtra	= 0;
	sd.cbWndExtra	= 0;
	sd.hInstance		= hinstance;
	sd.hIcon			= LoadIcon(NULL, IDI_APPLICATION);
	sd.hCursor		= LoadCursor(NULL, IDC_ARROW);
	sd.hbrBackground	= (HBRUSH)GetStockObject(BLACK_BRUSH);
	sd.lpszMenuName	= NULL;
	sd.lpszClassName	= g_szClassName;
	sd.hIconSm = LoadIcon(NULL, IDI_APPLICATION);
	if(!RegisterClassEx(&sd))
  {
    MessageBox(NULL, "Window Registration Failed!", "Error!",
      MB_ICONEXCLAMATION | MB_OK);
    return 0;
  }
if (!(hwnd = CreateWindowEx(NULL, g_szClassName,
						 "Demo",
						 WS_OVERLAPPEDWINDOW | WS_VISIBLE,
					 	 0,0,
						 WINDOW_WIDTH, WINDOW_HEIGHT,
						 NULL, NULL,hinstance, NULL)))
return(0);
global_hwnd = hwnd;
// initialize "ball" and "paddle"
initialize_ball();
initialize_paddle();
while(1)
	{
	if (PeekMessage(&msg,NULL,0,0,PM_REMOVE))
		{
			if (msg.message == WM_QUIT)
				break;
		TranslateMessage(&msg);
		DispatchMessage(&msg);
		}
		// draw ball, paddle, and score on the screen
		draw_score();
		draw_ball();
		draw_paddle();
		Sleep(10);
  }
return(msg.wParam);
}
// draw score in the upper left corner
void draw_score(void)
{
	sprintf(buffer, "Score: %d      ", (int)score);
	hdc= GetDC(global_hwnd);
	SetTextColor(hdc, RGB(0, 255, 0));
	SetBkColor(hdc, RGB(0, 0, 0));
	SetBkMode(hdc, OPAQUE);
	TextOut(hdc, 0, 0, buffer, strlen(buffer));
	ReleaseDC(global_hwnd, hdc);
}
// fill "ball" of struct Ball with information
void initialize_ball(void)
{
	ball.position_x = WINDOW_WIDTH / 2;
	ball.position_y = WINDOW_HEIGHT / 2;
	ball.radius = 10;
	ball.velocity_x = 4;
	ball.velocity_y = 2;
}
// draw ball to the screen
void draw_ball(void)
{
	hdc = GetDC(global_hwnd);
	// fill ball.rect with data
  ball.rect.left = ball.position_x - ball.radius;
  ball.rect.right = ball.position_x + ball.radius;
  ball.rect.top  = ball.position_y - ball.radius;
  ball.rect.bottom= ball.position_y + ball.radius;
  // erase ball using black brush
  SelectObject(hdc, GetStockObject(BLACK_BRUSH));
  SelectObject(hdc, black_pen);
  Ellipse(hdc, ball.rect.left, ball.rect.top, ball.rect.right, ball.rect.bottom);
  // move ball
  ball.position_x = ball.position_x + ball.velocity_x;
  ball.position_y = ball.position_y + ball.velocity_y;
	// tests if ball collides with left and right walls
  if (ball.position_x >= WINDOW_WIDTH - ball.radius || ball.position_x <= ball.radius)
    {
			ball.velocity_x = -ball.velocity_x;
			ball.position_x = ball.position_x + ball.velocity_x;
    }
	// test if ball collides with upper and bottom walls
  if (ball.position_y <= ball.radius)
    {
			ball.velocity_y = -ball.velocity_y;
			ball.position_y = ball.position_y + ball.velocity_y;
    }
	if (ball.position_y >= WINDOW_HEIGHT - 35)
    {
			ball.velocity_y = -ball.velocity_y;
			ball.position_y = ball.position_y + ball.velocity_y;
			score = score - 10;
    }
	// test if ball collides with paddle and adds 10 points if it is
	if(ball.velocity_y > 0
	  && paddle.upper_x_position < ball.position_x
	  && paddle.bottom_x_position > ball.position_x
	  && ball.position_y >= paddle.upper_y_position - paddle.height
	  && ball.position_y <= paddle.upper_y_position + paddle.height)
	{
		ball.velocity_y = -ball.velocity_y;
		ball.position_y = ball.position_y + ball.velocity_y;
		score = score + 10;
	}
	// set ball's y position if it goes too low
  if(ball.position_y < 10)
	{
		ball.position_y = 10;
		ball.velocity_y = -ball.velocity_y;
		ball.position_y = ball.position_y + ball.velocity_y;
	}
		// fill ball.rect with data
  ball.rect.left = ball.position_x - ball.radius;
  ball.rect.right = ball.position_x + ball.radius;
  ball.rect.top  = ball.position_y - ball.radius;
  ball.rect.bottom= ball.position_y + ball.radius;
	// draw ball with green brush
	old_brush = (HBRUSH)SelectObject(hdc, green_brush);
  Ellipse(hdc, ball.rect.left, ball.rect.top, ball.rect.right, ball.rect.bottom);
	SelectObject(hdc, old_brush);
  ReleaseDC(global_hwnd, hdc);
}
// fill "paddle" of stuct Paddle with information
void initialize_paddle(void)
{
	paddle.width = 70;
	paddle.height = 15;
	paddle.upper_x_position = WINDOW_WIDTH / 2;
	paddle.upper_y_position = WINDOW_HEIGHT - (4 * paddle.height);
	paddle.bottom_x_position = paddle.upper_x_position + paddle.width;
	paddle.bottom_y_position = WINDOW_HEIGHT - (5 * paddle.height);
	paddle.paddle_velocity = 8;
}
// draw paddle to the screen
void draw_paddle(void)
{
	hdc = GetDC(global_hwnd);
	// erase paddle with black brush
	old_brush = (HBRUSH)SelectObject(hdc, black_brush);
	Rectangle(hdc, paddle.upper_x_position,
			 paddle.upper_y_position,
			 paddle.bottom_x_position,
			 paddle.bottom_y_position);
	// check for users input and move paddle
	if(KEY_DOWN(VK_LEFT))
	{
		paddle.upper_x_position = paddle.upper_x_position - paddle.paddle_velocity;
		paddle.bottom_x_position = paddle.bottom_x_position - paddle.paddle_velocity;
	}
	// check for users input and move paddle
	if(KEY_DOWN(VK_RIGHT))
	{
		paddle.upper_x_position = paddle.upper_x_position + paddle.paddle_velocity;
		paddle.bottom_x_position = paddle.bottom_x_position + paddle.paddle_velocity;
	}
	// keep paddle in the window
	if(paddle.bottom_x_position >=WINDOW_WIDTH - 1)
	{
		paddle.bottom_x_position = WINDOW_WIDTH - 1;
		paddle.upper_x_position = WINDOW_WIDTH - paddle.width;
	}
	// keep paddle in the window
	if(paddle.upper_x_position <= 0)
	{
		paddle.upper_x_position = 0;
		paddle.bottom_x_position = paddle.width;
	}
	// draw paddle with red brush
	old_brush = (HBRUSH)SelectObject(hdc, red_brush);
	Rectangle(hdc, paddle.upper_x_position,
			 paddle.upper_y_position,
			 paddle.bottom_x_position,
			 paddle.bottom_y_position);
	SelectObject(hdc, old_brush);
	ReleaseDC(global_hwnd, hdc);
}
```

