---
layout:     post
title:      "python实现stft并绘制时频谱"
subtitle:   ""
date:       2020-1-18
author:     "HieDean"
header-img: "img/3.jpg"
tags:
    - 天大毕设
    - python
    - librosa
    - Matplotlib
---
### 音频处理库librosa的安装
官方文档中给出了非常详细的安装方法
[https://librosa.org/](https://librosa.org/)

### librosa.core.stft的使用
函数声明：
``librosa.core.stft(y, n_fft=2048, hop_length=None, win_length=None, window='hann', center=True, dtype=<class 'numpy.complex64'>, pad_mode='reflect')``
常用参数说明：
``y：输入的numpy数组，要求都是实数``
``n_fft：fft的长度，默认2048``
``hop_length：stft中窗函数每次步进的单位``
``win_length：窗函数的长度``
``window：窗函数的类型``
``return：一个1+n_fft/2*1+len(y)/hop_length的二维复数矩阵，其实就是时频谱``
参考：
[http://librosa.github.io/librosa/generated/librosa.core.stft.html#librosa.core.stft](http://librosa.github.io/librosa/generated/librosa.core.stft.html#librosa.core.stft)

### 绘图函数
主要用这两个
``matplotlib.pyplot.pcolormesh()``
``matplotlib.pyplot.colorbar()``

### demo
```python
import matplotlib.pyplot as plt
import librosa.core as lc
import numpy as np

fs = 16000
n_fft = 512

f = fs*np.array(range(int(1+n_fft/2)))/(n_fft/2)

path = "xxx.wav"

data = lc.load(path,sr=fs)

length = len(data[0])

spec = np.array(lc.stft(data[0], n_fft=512, hop_length=160, win_length=400, window='hann'))

plt.pcolormesh(np.array(range(int(length/160+1)))/fs, f, np.abs(spec))
plt.colorbar()
plt.title('STFT Magnitude')
plt.ylabel('Frequency [Hz]')
plt.xlabel('Time [sec]')
plt.tight_layout()
plt.show()
```
### 复杂一点的Matplotlib的使用
```python
import matplotlib.pyplot as plt
import numpy as np
import librosa.core as lc

noisy_path = './datasets/noisy_testset_wav/'
clean_path = './datasets/clean_testset_wav/'
wave_name = 'p257_059.wav'
# 准备x轴和y轴
x_axis = np.array(range(int(len(clean_wave)/160+1)))/fs
y_axis = fs*np.array(range(int(1+n_fft/2)))/(n_fft/2)
# 载入数据
noisy_wave = lc.load(noisy_path+wave_name,sr=16000)[0]
clean_wave = lc.load(clean_path+wave_name,sr=16000)[0]
# stft变换
noisy_spec = lc.stft(noisy_wave, n_fft=512, hop_length=160, win_length=400, window='hann')
clean_spec = lc.stft(clean_wave, n_fft=512, hop_length=160, win_length=400, window='hann')
# 定义一个figure，通俗讲就是一个窗口
figure = plt.figure() 
# 在这一个窗口中添加子图
axe_1 = figure.add_subplot(221)
axe_2 = figure.add_subplot(222)
axe_3 = figure.add_subplot(223)
axe_4 = figure.add_subplot(224)
# 给子图添加名称并绘制
axe_1.set_title('noisy_spec')
temp = axe_1.pcolormesh(y_axis, x_axis, np.abs(noisy_spec)) # 绘图
figure.colorbar(temp, ax=axe_1) # 给子图添加颜色渐变条

axe_2.set_title('clean_spec')
temp = axe_2.pcolormesh(y_axis, x_axis, np.abs(clean_spec))
figure.colorbar(temp, ax=axe_2)

axe_3.set_title('noisy_wave')
axe_3.plot(np.arange(len(noisy_wave))/fs, noisy_wave)

axe_4.set_title('noisy_wave')
axe_4.plot(np.arange(len(clean_wave))/fs, clean_wave)

plt.tight_layout() # 设置布局
plt.show() # 显示
```

### windows下使用librosa导入wav文件出现NoBackendError
`pip install ffmpeg`或`conda install ffmpeg`即可
