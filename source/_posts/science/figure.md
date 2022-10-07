---
title: matlab导出SCI论文图片
categories: sicence
type: sicence
description: 
top: 0
---
图片尺寸：单栏8cm，1.5栏14cm，双栏17cm
图片格式：tiff
字体大小：6-8pt
分辨率：彩色300dpi，黑白600dpi

画图代码示例
```
clc;
clear;
close all
x=1:1000;
plot(sin(2*pi*x/60));

% 绘图参数设置
font_Name = 'Times New Roman';  % 字体
figure_width = 8;               % 图片宽度 单栏 8cm ，1.5栏14cm   双栏17cm
figure_hight = 4.5;             % 图片高度，最高不超过20cm
figure_FontSize = 7;            % 图片中字体大小
line_Width = 1;                 % 线宽
xLabel = 'xlabel(m)';           % xlabel
yLabel = 'ylabel(m)';           % xlabel
figure_Name = 'testfigure';     % 图片名

%设置图片大小
set(gcf,'unit','centimeters','position',[50,50,figure_width,figure_hight]) 

% x,y标签
xlabel(xLabel,'FontName',font_Name,'FontSize',figure_FontSize)
ylabel(yLabel,'FontName',font_Name,'FontSize',figure_FontSize)

% axis
set(get(gca,'XLabel'),'FontSize',figure_FontSize,'Vertical','top'); 
set(get(gca,'YLabel'),'FontSize',figure_FontSize,'Vertical','middle'); 
set(findobj('FontSize',10),'FontSize',figure_FontSize); 
set(findobj(get(gca,'Children'),'LineWidth',0.5),'LineWidth',line_Width); 

% 图例
legend('FontName',font_Name,'FontSize',figure_FontSize,'LineWidth',line_Width);
legend('legend');      %设置图例
 
% 保存
print -dtiff -r300 testfigure
```

![testfigure][def]

[def]: /figure/testfigure.tif