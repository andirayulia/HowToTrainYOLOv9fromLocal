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

Sebelumnya buat terlebih dahulu folder datset dengan struktur seperti ini <br>
```python
dataset/
|-- train/
| |-- images/
| |-- labels/
|-- val/
| |-- images/
| |-- labels/
|-- test/
| |-- images/
| |-- labels/
|-- data.yaml
```


![image](https://github.com/user-attachments/assets/c23f8314-632f-4792-8352-2e0dbdc17e93) <br>
Folder train/ berisi gambar dan label yang digunakan untuk training, sedangkan folder val/ berisi gambar dan label untuk validation, dan test digunakan untuk testing. 

#Bagi gambar pada 3 folder tersebut seperti 70% TRAIN SET | 20% VALID SET | 10% TEST SET

#Buat juga **data.yaml** yang mendefinisikan lokasi gambar untuk training, validation, dan testing, serta jumlah kelas dan nama kelas yang ingin di deteksi

```python
train: /content/dataset/train/images
val: /content/dataset/valid/images
test: /content/dataset/test/images

nc: 5
names: ['helmet', 'no-helmet', 'no-vest', 'person', 'vest']
```

#Pada folder **labels** setiap gambar akan memiliki file **label.txt** yang berisi informasi bounding box yang telah dinormalisasi untuk semua objek di gambar tersebut supaya semua ukuran bounding boxnya selaras, general dan flexibel , dengan format seperti ini 
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

**Train Custom Model**
```python
%cd {HOME}/yolov9

!python train.py \
--batch 16 --epochs 25 --img 640 --device 0 --min-items 0 --close-mosaic 15 \
--data {HOME}/dataset/data.yaml \
--weights {HOME}/weights/gelan-c.pt \
--cfg models/detect/gelan-c.yaml \
--hyp hyp.scratch-high.yaml
```
![image](https://github.com/user-attachments/assets/7fc7bc78-a3fa-41c7-8620-1bbb6fd4a5e7)
![image](https://github.com/user-attachments/assets/8a29383a-d363-4bde-bd24-7677d99fd8c7)
![image](https://github.com/user-attachments/assets/1af1959b-dbcd-4717-bd5a-1aec49e0e781)
![image](https://github.com/user-attachments/assets/41141e98-913c-49de-9af9-a9b25f16bc76)

**Validate Custom Model**

```python
%cd {HOME}/yolov9

!python train.py \
--batch 16 --epochs 25 --img 640 --device 0 --min-items 0 --close-mosaic 15 \
--data {HOME}/dataset/data.yaml \
--weights {HOME}/weights/gelan-c.pt \
--cfg models/detect/gelan-c.yaml \
--hyp hyp.scratch-high.yaml
```
![image](https://github.com/user-attachments/assets/91a9e47f-e8c1-467d-930d-dfe466730ed1)

**Inference with Custom Model**

```python
!python detect.py \
--img 1280 --conf 0.1 --device 0 \
--weights {HOME}/yolov9/runs/train/exp2/weights/best.pt \
--source {HOME}/dataset/test/images
```
```python
import glob

from IPython.display import Image, display

for image_path in glob.glob(f'{HOME}/yolov9/runs/detect/exp/*.jpg')[:2]:
      display(Image(filename=image_path, width=600))
```
![image](https://github.com/user-attachments/assets/4946fa10-9063-401a-b8e4-52fa64e6fa32)
![image](https://github.com/user-attachments/assets/b94f4063-26e0-481e-9df8-11425c5ada60)




**Labeling Dataset for Object Detector**

Labeling dapat dilakukan menggunakan LabelImg, untuk class yang digunakan dapat dicustom atau diubah-ubah pada predefined_classes.txt sehingga urutan nomor class nya akan sesuai dengan data.yaml

![image](https://github.com/user-attachments/assets/0136cd2e-a10e-4713-be43-1d9665b14c4f)


![Screenshot 2024-09-08 233221](https://github.com/user-attachments/assets/cb8b1163-8ad2-48ee-b095-e936dc054725)
![Screenshot 2024-09-08 233409](https://github.com/user-attachments/assets/73513985-3661-49d5-8c20-720140f8e24c)
![Group 333](https://github.com/user-attachments/assets/bc13c884-6a19-435c-afec-d0fc25fcfe1d)

```python
xcenter = (xmin + w/2) / w_img
ycenter = (ymin + h/2) / h_img
w = w / w_img
h = h / h_img
```
```python
<class_id> <x_center> <y_center> <width> <height>
```
Untuk labeling yang mendapatkan langsung format seperti ini, dapat diatur menggunakan format YOLO

![Screenshot 2024-09-08 233443](https://github.com/user-attachments/assets/010b43b1-2bbb-44e2-a385-9f9862b1946a)
