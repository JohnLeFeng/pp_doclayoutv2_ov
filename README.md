# PP-DocLayoutV2 OpenVINO Inference

OpenVINO-based inference implementation for PP-DocLayoutV2 document layout detection.

## 📋 Introduction

This project provides a complete implementation for running PP-DocLayoutV2 document layout detection model using OpenVINO inference engine. PP-DocLayoutV2 is a document layout analysis model provided by PaddleOCR, capable of detecting various layout elements in documents, such as titles, paragraphs, tables, images, etc.

## ✨ Features

- ✅ Complete OpenVINO IR model inference implementation
- ✅ Fully consistent with PaddleOCR preprocessing and post-processing logic
- ✅ Support for multiple inference devices: CPU, GPU, AUTO
- ✅ Command-line parameterization for easy integration and use
- ✅ Automatic saving of detection results (JSON and visualization images)
- ✅ Support for 25 types of document layout element detection

## 🔧 Requirements

- Python >= 3.7
- OpenVINO >= 2023.0
- OpenCV (cv2)
- NumPy
- PIL (Pillow)

## 📦 Installation

```bash
# Install OpenVINO
pip install openvino

# Install other dependencies
pip install opencv-python numpy pillow
```

## 🚀 Quick Start

### Basic Usage

```bash
python ov_infer.py \
    --model_path pp_doclayoutv2.xml \
    --image_path layout.jpg \
    --output_dir ./output
```

### Complete Parameter Example

```bash
python ov_infer.py \
    --model_path pp_doclayoutv2.xml \
    --image_path layout.jpg \
    --output_dir ./output_ov \
    --device GPU \
    --threshold 0.5
```

## 📖 Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `--model_path` | str | ✅ | - | OpenVINO IR model path (.xml file) |
| `--image_path` | str | ✅ | - | Input image path |
| `--output_dir` | str | ❌ | `./output_ov` | Output directory |
| `--device` | str | ❌ | `GPU` | Inference device: `CPU`, `GPU`, `AUTO` |
| `--threshold` | float | ❌ | `0.5` | Detection confidence threshold |

## 📊 Supported Layout Elements

The model can detect the following 25 types of document layout elements:

- `abstract` - Abstract
- `algorithm` - Algorithm
- `aside_text` - Aside text
- `chart` - Chart
- `content` - Content
- `display_formula` - Display formula
- `doc_title` - Document title
- `figure_title` - Figure title
- `footer` - Footer
- `footer_image` - Footer image
- `footnote` - Footnote
- `formula_number` - Formula number
- `header` - Header
- `header_image` - Header image
- `image` - Image
- `inline_formula` - Inline formula
- `number` - Number
- `paragraph_title` - Paragraph title
- `reference` - Reference
- `reference_content` - Reference content
- `seal` - Seal
- `table` - Table
- `text` - Text
- `vertical_text` - Vertical text
- `vision_footnote` - Vision footnote

## 📁 Output Format

### JSON Output

Detection results are saved as JSON files in the following format:

```json
{
  "res": {
    "input_path": "layout.jpg",
    "page_index": null,
    "boxes": [
      {
        "cls_id": 0,
        "label": "text",
        "score": 0.95,
        "coordinate": [xmin, ymin, xmax, ymax]
      },
      ...
    ]
  }
}
```

### Visualization Image

Detection results are saved as visualization images, including:
- Detection box drawing
- Category labels and confidence scores
- Different colors to distinguish different categories

## 🔍 Comparison with PaddleOCR

This project provides `paddle_infer.py` as a reference implementation using PaddleOCR official API for inference. The implementation of `ov_infer.py` is fully consistent with PaddleOCR's preprocessing and post-processing logic, ensuring consistency of inference results.

## 📝 Code Examples

### Python API Usage

```python
from ov_infer import paddle_ov_doclayout

result = paddle_ov_doclayout(
    model_path="pp_doclayoutv2.xml",
    image_path="layout.jpg",
    output_dir="./output",
    device="GPU",
    threshold=0.5
)

print(f"Detected {len(result.boxes)} layout elements")
for box in result.boxes:
    print(f"{box['label']}: {box['score']:.3f} at {box['coordinate']}")
```

## 🛠️ Technical Details

### Preprocessing Pipeline

1. BGR → RGB conversion
2. Resize to 800×800 (using INTER_CUBIC interpolation)
3. Normalize to [0, 1] (divide by 255.0)
4. HWC → CHW transpose
5. Add batch dimension

### Post-processing Pipeline

1. Output format conversion (handle DETR format output)
2. Threshold filtering
3. NMS (Non-Maximum Suppression)
4. Large image filtering
5. Layout box merging (optional)
6. Ordered detection sorting
7. Coordinate expansion (unclip, optional)
8. Format output

## 📄 License

This project is based on PaddleOCR's PP-DocLayoutV2 model. Please follow the corresponding open source license.

## 🙏 Acknowledgments

- [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) - Provides the original model and reference implementation
- [OpenVINO](https://github.com/openvinotoolkit/openvino) - Provides high-performance inference engine

## 📧 Contact

For questions or suggestions, please submit an Issue or Pull Request.
