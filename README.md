# HowToTrainYOLOv9fromLocal

**Preparation**

Membuat konstanta (HOME) untuk menyimpan path direktori utama
```python
import os
HOME = os.getcwd()
print(HOME)
```

**Clone and Install**

Melakukan kloning untuk mendapatkan seluruh repository yolov9 dan installasi requirements 
```python
!git clone https://github.com/SkalskiP/yolov9.git
%cd yolov9
!pip install -r requirements.txt -q
```

**Download Model Weights**
```python
!wget -P {HOME}/weights -q https://github.com/WongKinYiu/yolov9/releases/download/v0.1/gelan-c.pt
```

**Upload Dataset**

**PREPARE FOLDER DATASET**

Sebelumnya buat terlebih dahulu folder datset dengan struktur seperti ini 
![image](https://github.com/user-attachments/assets/c23f8314-632f-4792-8352-2e0dbdc17e93) <br>
Folder train/ berisi gambar dan label yang digunakan untuk training, sedangkan folder val/ berisi gambar dan label untuk validation, dan test digunakan untuk testing. 

#Bagi gambar pada 3 folder tersebut seperti 70% TRAIN SET | 20% VALID SET | 10% TEST SET

#Buat juga **data.yaml** yang mendefinisikan lokasi gambar untuk training, validation, dan testing, serta jumlah kelas dan nama kelas yang ingin di deteksi

```python
train: ../train/images
val: ../val/images
test: ../test/images

nc: 5
names: ['helmet', 'no-helmet', 'no-vest', 'person', 'vest']

```

#Pada folder **labels** setiap gambar akan memiliki file **label.txt** yang berisi informasi bounding box yang telah dinormalisasi untuk semua objek di gambar tersebut, dengan format seperti ini 
```python
<class_id> <x_center> <y_center> <width> <height>
```
![ppe_1326_jpg rf 5cf36959482eecd24a430307c73ac195](https://github.com/user-attachments/assets/0dbad743-c8a2-4911-8388-6f207bf4b0ac)

![image](https://github.com/user-attachments/assets/1a0c2f82-05d3-47ef-bd0a-90fa759c443e)

**UPLOAD DATA**
compress dulu folder ke dalam ZIP file, untuk memudahkan upload 
```python
from google.colab import files
uploaded = files.upload()
```
kemudian unZIP file
```python
from google.colab import files
uploaded = files.upload()
```

**Train**
```python
%cd {HOME}/yolov9

!python train.py \
--batch 16 --epochs 25 --img 640 --device 0 --min-items 0 --close-mosaic 15 \
--data {dataset.location}/data.yaml \
--weights {HOME}/weights/gelan-c.pt \
--cfg models/detect/gelan-c.yaml \
--hyp hyp.scratch-high.yaml
```
