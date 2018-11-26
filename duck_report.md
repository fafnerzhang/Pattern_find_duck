環境：jupyter notebook 
程式架構：此次寫了兩支程式**duck_test** 與**duck_final**，duck_test只利用一張duck_pixel的圖片建構兩類之高斯模型，即輸出最終預測結果，duck_final可自動讀入多張duck_pixel與non_duck_pixel建構高斯模型，再輸出預測結果。
# **Duck_test**
 ![](https://i.imgur.com/n1tAyVe.png)

1. 利用小畫家切下full_duck中有duck的部分，將有duck的影像讀入並轉為灰階，將相素值沒那麼大（較暗沉）的點設為０，結果如下，會留下duck_body的pixel
![](https://i.imgur.com/KogC0Np.png)
2. 利用處理完的圖片建立Bayesian classifier需要的label(y_train)如果圖片的pixel大於0　,　y_train即為1，代表其為duck_pixel
接著讀入原本還未處理過的duck_pixel(轉灰階)建立x_train，最後丟入bayesian classifier建立模型
![](https://i.imgur.com/OrEfjpO.png)
3. 讀入full_duck_pixel並轉換為可分類的資料維度
![](https://i.imgur.com/nCSmWmj.png)
利用訓練完的bayesian classifier預測處理完的full_duck其label,利用for迴圈將值為１的資料轉為較大數值，並轉為二維陣列，儲存輸出結果
![](https://i.imgur.com/QRIPkVN.png)
# Duck_final
大致上與duck_test相同，有鑑於本來架構讀取圖片很麻煩需要手打程式碼，因此增加了可讓程式自動讀入training data 的處理，並可印出兩類prior的高斯分布。
1.	首先給定總共有幾筆有duck_pixel與non_duck_pixel
2.	duck_pixel的處理，將圖片讀入後整理為x_train的資料維度丟入x_train,另外建立一變數y_train_duck儲存此筆duck_pixel的label，最後丟入y_train，即進行完此次duck_pixel的處理
![](https://i.imgur.com/rqjzW7c.png)

3.	non_duck_pixel的處理，與duck_pixel相同，但不需做轉換y_train的處理，只需將y_train設為維度與輸入圖片相同的0陣列即可，接著將處理完的兩類樣本丟入bayesian classifier裡做訓練
![](https://i.imgur.com/6ypf0w5.png)

4.	和duck_test類似，讀入full_duck做預測產生圖片，利用預測出來的result處理full_duck，如果result為0，對應的full_duck像素也會被設為0
![](https://i.imgur.com/o7KI408.png)	
5.	最後印出兩類平均與標準差，繪製高斯曲線圖
![](https://i.imgur.com/HxS7ctc.png)
![](https://i.imgur.com/a47t7eT.png)
# Result
這次的作業使用第一支程式duck_test使用duck0做為樣本產生最一開始的預測結果 **sample_only_duck_pixel_output.jpg**,與其之高斯曲線,如下圖
![](https://i.imgur.com/9rbAMNJ.jpg)
model theta
 [[112.59011086]
 [245.00478017]]
model sigma
 [[952.82126832]
 [ 60.07281525]]
training sample
 [75052. 15899.]

再來使用duck_final利用三種不同數量的sample輸出三種預測結果與gaussin distribuction,以下為三類之輸出之gaussin distribuction的資料,圖片與繪製出的高斯曲線在資料夾內都可以找到
**Sample1**(8筆non_duck,5筆duck):
	training sample
(non_duck0~non_duck7) (duck0~duck4) 
![](https://i.imgur.com/ZPyK35W.jpg)


**Sample2**(8筆non_duck,7筆duck): 
training sample
 (non_duck0~non_duck7) (duck0~duck4+duck_paint+duck_black)
![](https://i.imgur.com/P2JVcdO.jpg)

**Sample3**(12筆non_duck,5筆duck):
training sample
	 (non_duck0~non_duck11) (duck0~duck4)
	 [19787268.   351404.]
![](https://i.imgur.com/DFpuma3.jpg)



||sample|sample1|sample2|sample3|
|-|-|-|-|-|
|non_duck_theta|112.5901|106.93681475|102.42188502|114.72653587|
|non_duck_sigma|30.86780|42.594369470|46.999536354|45.4544637075|
|duck_theta|245.00478|246.78793639|246.57023706|246.78793639|
|duck_sigma|7.7506654|7.4693665179|7.41235379|7.46936651|
|non_duck|0|8|8|12|
|duck|1|5|7|5|
# Discussion on the result
1. 在第一個sample只使用一張包含duck_pixel的圖片進行預測,可以看到其高斯曲線圖分得非常開,non_duck的sigma也是四種輸出中最小的,推測是non_duck樣本數很少所以才讓其sigma分布相較另外三者來的小

2. sample1與sample相比較,non_duck_pixel與duck_pixel的圖片,可以看到兩類分布的高斯曲線有重疊,non_duck類的sigma變大,是我們增加non_duck_pixel的訓練樣本所導致的,再者duck_pixel的sigma有下降,代表新丟入的duck_pixel有使duck類的model變得更為集中

3. sample2與sample1相比較,多丟入了兩張duck_pixel的圖片,可以看到duck類的sigma有下降,代表多丟入的duck_pixel圖片有使duck類的model更為收斂集中,但是sample2中non_duck的高斯分布不知道發生什麼事,樣本數一樣但出來的theta與sigma都跟sample1有些差距,推測應該是被duck_pixel圖片中標為0,屬於non_duck類的pixel影響

4. sample3與sample1相比較,多丟入5張non_duck_pixel的圖片,sample3的non_duck高斯曲線右移許多,且sigma變大,相較於此,因為duck_pixel是一樣的,duck類的高斯曲線是一模一樣

綜合上述可以觀察到幾點
1. non_duck像素分布的範圍應該相當廣泛,當增加non_duck_pixel的圖片進入模型時,皆會使的non_duck類的高斯曲線越來越平坦,不過這也可能是因為我新增non_duck_pixel圖片時都是使用鵝卵石之類pixel值較大的樣本造成的,也就是訓練樣本可能有偏頗的蒐集

2. duck的pixel相對較好辨認,分布範圍較小,這從sample2,有最多張duck_pixel圖片的model,其標準差最小可以觀察的出來

3. 因為duck_pixel範圍很集中,一旦non_duck_pixel的值靠近duck_pixel便很容易被認錯

4. 最後輸出的圖片其實很難評比哪個較好,每種樣本數量多寡皆會影響辨識結果,如果你的non_duck_pixel很少,相對的duck_pixel可能不會那麼完整,而如果duck_pixel很完整,那麼你的non_duck_pixel便會很多

5. 不過可以確定的是兩種樣本都要有,辨識model才會比較完整,不能只用一種duck_pixel的圖片進行辨識
# Summary
**學到的事**
1. 在這次作業中一開始還以為要從圖片中抓特徵到bayesian中辨識,還找了很多抓取feature的資料,後來仔細看老師給的文本才發現要辨識的是duck的pixel,所以只需要將圖片陣列轉為二維,包含一特徵(pixel)的陣列做辨識即可,下次做事情會先搞清楚要做什麼再開始,不然實在多花很多時間

2. 對numpy有點熟悉感了
3. 看文檔的時候要看清楚,這sklearn的beyasian classifier呼叫sigma結果出來是variance....到底是什麼奇怪的規則,讓我多花了點時間把正常的高斯曲線畫出來
![](https://i.imgur.com/p77Bs6m.png)
