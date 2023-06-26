학교 건물들이 벽돌로 지어져 있어, 벽돌의 층 수를 구해 건물의 높이를 예측하는 프로젝트입니다.

실제 학교 건물들의 사진을 사용하였습니다. Canny Edge Detection 알고리즘을 활용해 이미지 전처리를 진행하였습니다.

그 후 임의의 세로 선분을 그어 나온 데이터 값에 \(벽돌 사이는 높은 값, 벽돌 부분은 낮은 값\) 보간법을 적용하여 로컬 맥시멈 갯수를 찾아 층 수를  구했습니다.

로컬 맥시멈 값을 원본 이미지에 그려 층의 결과를 확인하였습니다.



# 라이브러리 불러오기




```python
import numpy as np
import numpy.linalg as la
import matplotlib.pyplot as plt
import scipy.ndimage as ndi
from skimage.io import imread, imsave
from skimage.color import rgb2gray
%matplotlib inline 
```

# 학교 강당 건물 사진 불러오기

University of Illinois Urbana-Champaign의 Foellinger Auditorium의 건물 사진를 촬영하고 로드하였습니다.


```python
FA_filename = 'original_image.jpg'
FA_color = imread(FA_filename)
```

<img src="C:\jaydenryou-github-blog\JaydenRyou.github.io\images\2023-06-23-counting_bricks\original_image.jpg" alt="original_image" style="zoom:50%;" />

# Gray Scale

skimage.io의 rgb2gray를 사용해 RGB 채널을 Gray scale로 변환하였습니다.


```python
FA_gray = rgb2gray(FA_color)
```

<img src="C:\jaydenryou-github-blog\JaydenRyou.github.io\images\2023-06-23-counting_bricks\gray_image.jpg" alt="gray_image" style="zoom:50%;" />

# Gaussian Kernel

5 x 5 Gaussian Kernel 만든 후 Gray scale 된 이미지와 convolutional operation을 통해 이미지 노이즈 제거하였습니다.


```python
def gaussian_kernel(size, sigma = 1):
    size = int(size)//2
    x, y = np.mgrid[-size:size+1, -size:size+1]
    normal = 1/(2.0 *np.pi*sigma**2)
    g = np.exp(-((x**2 + y**2) / (2.0*sigma**2))) * normal
    return g
```


```python
gaussian_kernel(5)
```




    array([[0.00291502, 0.01306423, 0.02153928, 0.01306423, 0.00291502],
           [0.01306423, 0.05854983, 0.09653235, 0.05854983, 0.01306423],
           [0.02153928, 0.09653235, 0.15915494, 0.09653235, 0.02153928],
           [0.01306423, 0.05854983, 0.09653235, 0.05854983, 0.01306423],
           [0.00291502, 0.01306423, 0.02153928, 0.01306423, 0.00291502]])




```python
kernel = gaussian_kernel(5)

FA_blur = ndi.convolve(FA_gray, kernel)
```

<img src="C:\jaydenryou-github-blog\JaydenRyou.github.io\images\2023-06-23-counting_bricks\gaussian_kernel.jpg" alt="gaussian_kernel" style="zoom:50%;" />

# Sobel filter

Sobel filter를 사용해 가로, 세로 축 방향의 엣지 값을 극대화하였습니다.


```python
def sobel_filters(img):
    Kx = np.array([[-1, 0, 1], [-2, 0, 2], [-1, 0, 1]], np.float32)
    Ky = np.array([[1, 2, 1], [0, 0, 0], [-1, -2, -1]], np.float32)
    
    Ix = ndi.convolve(img, Kx)
    Iy = ndi.convolve(img, Ky)
    
    G = np.hypot(Ix, Iy)
    G = G / G.max() * 255
    theta = np.arctan2(Iy, Ix)
    
    return (G, theta)
```


```python
FA_intensity, D = sobel_filters(FA_blur)
```

<img src="C:\jaydenryou-github-blog\JaydenRyou.github.io\images\2023-06-23-counting_bricks\sobel_filter.jpg" alt="sobel_filter" style="zoom:50%;" />

# Non Maximum Suppression

NMS를 사용해 엣지 부분을 얇게 만들었습니다.


```python
def non_max_suppression(img, D):
    M, N = img.shape
    Z = np.zeros((M,N), dtype=np.int32)
    angle = D * 180. / np.pi
    angle[angle < 0] += 180

    
    for i in range(1,M-1):
        for j in range(1,N-1):
            try:
                q = 255
                r = 255
                
                #angle 0
                if (0 <= angle[i,j] < 22.5) or (157.5 <= angle[i,j] <= 180):
                    q = img[i, j+1]
                    r = img[i, j-1]

                #angle 90
                elif (67.5 <= angle[i,j] < 112.5):
                    q = img[i+1, j]
                    r = img[i-1, j]


                if (img[i,j] >= q) and (img[i,j] >= r):
                    Z[i,j] = img[i,j]
                else:
                    Z[i,j] = 0

            except IndexError as e:
                pass
    
    return Z
```


```python
FA_nms = non_max_suppression(FA_intensity, D)
```

<img src="C:\jaydenryou-github-blog\JaydenRyou.github.io\images\2023-06-23-counting_bricks\nms.jpg" alt="nms" style="zoom:50%;" />

# 층의 갯수 구하기

임의의 세로 선분 그어 값을 구했습니다.

```python
temp = FA_nms[:, 600]
X = np.arange(len(temp))
```

<img src="C:\jaydenryou-github-blog\JaydenRyou.github.io\images\2023-06-23-counting_bricks\temp.png" alt="temp" style="zoom:50%;" />


```python
import matplotlib.pyplot as plt
from scipy import interpolate
from scipy.ndimage import gaussian_filter1d
from scipy.signal import find_peaks

spl = interpolate.UnivariateSpline(X, temp)
n = FA_nms.shape[0]

plt.figure(dpi = 300)
plt.plot(X, temp, 'bo', markersize = 0.5, label = 'Original')
spl.set_smoothing_factor(1)
y = spl(X)
y_smooth = gaussian_filter1d(y, sigma = 2.5)
peaks, _ = find_peaks(y_smooth)
plt.plot(X, y, 'r-', lw=0.3, label = 'Interpolated data')
plt.plot(X, y_smooth, 'g-', lw = 1, label = 'Gaussian filtered data')
plt.plot(peaks, y_smooth[peaks], 'x')
plt.legend(fontsize = 8, borderpad=0.01)

print('''Number of bricks' layers:''', len(peaks))
```

    Number of bricks' layers: 112  

원본 값(파란색 점)에 보간 법을 적용한 것은 빨간색 선입니다. 

그 후 Gaussian 1d filter를 적용해 초록색 선을 구했습니다.

그 후 find_peaks 함수를 사용해 로컬 맥시멈 값을 (파란색 X) 구했습니다. 

112 개의 로컬 맥시멈 값을 구했습니다. (112 개의 벽돌 층을 의미)

<img src="C:\jaydenryou-github-blog\JaydenRyou.github.io\images\2023-06-23-counting_bricks\interpolated.png" alt="interpolated" style="zoom:50%;" />


```python
import cv2
x_start = 0
x_end = FA_nms.shape[1]
y_start = 0
y_end = FA_nms.shape[0]

result = FA_color.copy()

for peak in peaks:
    cv2.line(result, (x_start, peak), (x_end, peak), (0, 255, 0), 1)
cv2.line(result, (600, y_start), (600, y_end), (255, 0, 0), 2)
```

앞서 구한 로컬 맥시멈 값을 원본 이미지에 초록색 가로선을 그었습니다.

빨간 세로 선은 임의로 그은 선입니다.



<img src="C:\jaydenryou-github-blog\JaydenRyou.github.io\images\2023-06-23-counting_bricks\result.jpg" alt="result" style="zoom:50%;" />
