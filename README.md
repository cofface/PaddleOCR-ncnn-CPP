This repository provides C++ implementations of PP-OCRv3/4/5/6, inspired by the official [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) project and using [ncnn](https://github.com/Tencent/ncnn) for inference.

The Release page contains a complete project archive that includes resources like models and images required to run the demo.

## Demo

<p align="center">
  <img src="https://cdn.jsdelivr.net/gh/Avafly/ImageHostingService@master/uPic/PixPin_2025-08-22_23-55-24.png" width = "800">
</p>

## Usage

```bash
./main ../config.json ../images/ocr_img1.png
```

## Requirements

* OpenCV 4.11.0
* ncnn 20241226
* OpenMP 2.0+

## Benchmarks

I ran benchmarks on a VPS using `ocr_img1.png` (simple) and `ocr_img3.png` (complex). You can find those images inside the release page `archive.tar.gz`.

CPU: 2 x Intel(R) Xeon(R) Platinum (2) @ 2.50 GHz

|     Models      | Latency / Peak memory for ocr_img1 | Latency / Peak memory for ocr_img3 |
| :-------------: | :--------------------------------: | :--------------------------------: |
| PP-OCRv3 mobile |        88.09 ms / 106.2 MB         |        926.34 ms / 228.3 MB        |
| PP-OCRv4 mobile |        90.44 ms / 97.57 MB         |       1005.45 ms / 211.5 MB        |
| PP-OCRv5 mobile |        92.17 ms / 106.1 MB         |       1062.56 ms / 292.2 MB        |
| PP-OCRv5 server |       3948.50 ms / 1.545 GB        |         11172.83 ms / OOM          |
|  PP-OCRv6 tiny  |        67.45 ms / 62.22 MB         |        420.28 ms / 156.7 MB        |
| PP-OCRv6 small  |        139.36 ms / 127.7 MB        |       1590.45 ms / 351.4 MB        |
| PP-OCRv6 medium |        584.79 ms / 425.3 MB        |       6298.11 ms / 706.2 MB        |

## Tested on

* macOS 15
* Debian 12
* Windows 10/11

## Notes

Enabling FP16 may be faster but can cause NaN results on some devices. Edit `config.json` to enable or disable FP16.

## Implementation References

https://github.com/MhLiao/DB

https://github.com/nihui/ncnn-android-ppocrv5

https://github.com/FeiGeChuanShu/ncnn_paddleocr

https://github.com/PaddlePaddle/PaddleOCR



///////////////////////////////////////////////////模型转换参考
guidelines for converting PPOCRv5 models(https://github.com/nihui/ncnn-android-ppocrv5#guidelines-for-converting-ppocrv5-models)

1.install paddleocr:
pip install paddlepaddle==3.0.0
pip install paddleocr==3.0.0
paddlex --install paddle2onnx

2.test paddleocr and export onnx models:
paddleocr ocr -i test.png
paddlex --paddle2onnx --paddle_model_dir ~/.paddlex/official_models/PP-OCRv5_mobile_det --onnx_model_dir PP-OCRv5_mobile_det
paddlex --paddle2onnx --paddle_model_dir ~/.paddlex/official_models/PP-OCRv5_mobile_rec --onnx_model_dir PP-OCRv5_mobile_rec

3.convert onnx models to ncnn with pnnx tool:
pip install -U pnnx
pnnx PP-OCRv5_mobile_det.onnx inputshape=[1,3,320,320] inputshape2=[1,3,256,256]
pnnx PP-OCRv5_mobile_rec.onnx inputshape=[1,3,48,160] inputshape2=[1,3,48,256]
pnnx PP-OCRv5_server_det.onnx inputshape=[1,3,320,320] inputshape2=[1,3,256,256] fp16=0
pnnx PP-OCRv5_server_rec.onnx inputshape=[1,3,48,160] inputshape2=[1,3,48,256] fp16=0
