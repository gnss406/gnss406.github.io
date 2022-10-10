---
title: 人工智能学习笔记：MATLAB实现简单的图像分类学习
categories: sicence
type: sicence
description: 
top: 0
---
参考《深度学习的数学》，采用MATLAB实现图像分类的小程序，去理解深度学习中的相关知识。

**问题1：深度学习**采用待估参数w和b，通常采用随机函数生成，但不同的随机数得到的w和b是不同的，可视为初值不同，所收敛的位置也不相同，**那么两个由不同初值得到的网络是否具有同样的拟合能力呢？**

**问题2：深度学习会否由于具有较高的拟合特性而影响粗差的判别？**

**问题3：样本中的粗差是否都应剔除？**

**问题4：非线性参数估计方法如何在深度学习中得到应用？**

[dataAI](/source/Document/dataAI.mat "测试数据1")

[dataAI1](/source/Document/dataAI.mat "测试数据2")

MATLAB代码
```
clc
clear
close all

% 读取数据 dataAI
load dataAI1.mat  % dataAI

ndata = length(label);     %样本个数

rng(15); %设置种子
nnum = fix(ndata*0.8); %训练集
list = randperm(ndata,nnum);

totalData = data;
totalLabel = label;

data = totalData(:,:,list);
label = totalLabel(:,list);

csdata = totalData;
csdata(:,:,list)=[];

cslabel = totalLabel;
cslabel(:,list) = [];
ndata = nnum;

% 预设网络
% nnodel = [12; 3; 2 ];          % 定义每一层的节点数 for dataAI
nnodel = [36; 4; 3 ];          % 定义每一层的节点数 for dataAI1

nlay = length( nnodel );      % AI层数

for i=1 : nlay - 1
    tmplay = rand( nnodel( i ) + 1, nnodel( i +1 ))';
    lay(i).wb = tmplay ;
end

step = 0.2;     % 步长 / 学习率
iter = 1;
while 1
    totalc = 0;
    % dwb
    for n =1 : nlay - 1
        tmpdwb = zeros(nnodel( n +1 ), nnodel( n ) + 1);
        dwb(n).dwb = tmpdwb;
    end
    
    for i = 1 : ndata
        
        tmpa = data(:,:,i);     %读取数据   输入层的a为输入数据
        
        % 定义结构体 存储各层的
        tmp(1).z = [];
        tmp(1).a = tmpa(:);     %vec
        tmp(1).a1 = [];
        
        % 计算 隐藏层和输出层 各层的 z  a  a' (预测过程)
        for n = 2 : nlay
            tmp(n).z = lay(n-1).wb(:,1:end-1) * tmp(n-1).a + lay(n-1).wb(:,end);
            tmp(n).a = 1./(1+exp(-tmp(n).z));
            tmp(n).a1 = tmp(n).a .* (1-tmp(n).a);
        end
        
        tdata(i).data = tmp;
        
        % 预测结果的正确性
        yc(i) =norm((tmp(n).a==max(tmp(n).a)) - label(:,i));
        
        % 预测误差
        tmpc =  tmp(n).a - label(:,i);
        tdata(i).C = tmpc'*tmpc/2;
        totalc =totalc+tdata(i).C; 
        
        % wb修正
        % step 1 计算各层的神经元误差delta 由输出层进行反向递推（反向误差传播）
        tmp(n).delta = tmpc.*tmp(n).a1;         % P149 eq(7)   输出层
        for n = nlay-1 : -1 : 2
            tmp(n).delta = (lay(n).wb(:,1:end-1)'*tmp(n+1).delta) .* tmp(n).a1;  %隐藏层 P151 eq(16)
        end
        
        % step 2. 计算各层wb的偏导数 P144 eq(11)
        for n = 1 : nlay - 1
            dwb(n).dwb =dwb(n).dwb + tmp(n+1).delta * [tmp(n).a ; 1]';
        end
    end
    
    % 梯度下降法更新wb
    for n =1 : nlay - 1
        lay(n).wb = lay(n).wb - step * dwb(n).dwb ;
    end

    if (ndata-sum(~yc) == 0)&& iter>200
        break;
    end
    % 记录每一次迭代的误差
    err(iter) = totalc;
    errrate(iter) = (ndata-sum(~yc))/ndata*100;
    iter = iter + 1;
end

yyaxis left
plot(err);ylabel('totalC')
hold on;
yyaxis right
plot(errrate);ylabel('errRate(%)')
```
![testfigure][def]

[def]: /figure/ai2.tif
