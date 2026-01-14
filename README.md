# PP-DocLayoutV2 OpenVINO Inference

OpenVINO-based inference implementation for PP-DocLayoutV2 document layout detection.

## 📋 Introduction

This project provides a complete implementation for running PP-DocLayoutV2 document layout detection model using OpenVINO inference engine. PP-DocLayoutV2 is a document layout analysis model provided by PaddleOCR, capable of detecting various layout elements in documents, such as titles, paragraphs, tables, images, etc.

## ✨ Features

- ✅ Complete OpenVINO IR model inference implementation
- ✅ Fully consistent with PaddleOCR preprocessing and post-processing logic
- ✅ Support for multiple inference devices: CPU, GPU, NPU, AUTO
- ✅ **Automatic model download from ModelScope** (no manual download required)
- ✅ Support for multiple model precisions: FP16, FP32, combined models
- ✅ Command-line parameterization for easy integration and use
- ✅ Automatic saving of detection results (JSON and visualization images)
- ✅ Support for 25 types of document layout element detection

## 📦 Installation

```bash
pip install -r requirements.txt
```

**Note:** For automatic model download feature, ensure `modelscope` is installed:
```bash
pip install modelscope
```

## 📥 Model Download

### Automatic Download (Recommended)

The script supports automatic model download from ModelScope. When no model path is specified, the model will be automatically downloaded from ModelScope, and the specific model variant will be selected based on the `--precision` parameter:

```bash
# Auto-download FP32 model (default)
python ov_infer.py --image_path layout.jpg

# Auto-download FP16 model (faster, lower memory)
python ov_infer.py --image_path layout.jpg --precision fp16

# Auto-download combined FP32 model
python ov_infer.py --image_path layout.jpg --precision combined_fp32
```

**Note:** Automatic download requires `modelscope` package. Install with: `pip install modelscope`

### Manual Download

If you prefer to download manually, the OpenVINO IR model files are available on ModelScope:

**Model Location:** [PP-DocLayoutV2-ov on ModelScope](https://www.modelscope.cn/models/zhaohb/PP-DocLayoutV2-ov/summary)

After downloading, specify the path to the `.xml` file using the `--model_path` parameter.

### Available Model Precisions

The repository provides multiple model variants:

- **FP32** (`pp_doclayoutv2_f32.xml`): Full precision, highest accuracy (default)
- **FP16** (`pp_doclayoutv2_f16.xml`): Half precision, faster inference, lower memory usage
- **Combined FP32** (`pp_doclayoutv2_f32_combined.xml`): FP32 model with merged batch size and boxes nodes
- **Combined FP16** (`pp_doclayoutv2_f16_combined.xml`): FP16 model with merged batch size and boxes nodes

Use the `--precision` parameter to select the desired model variant.

## 🚀 Quick Start

### Basic Usage (Auto-download Model)

```bash
# Simplest usage - automatically downloads FP32 model
python ov_infer.py --image_path layout.jpg

# Auto-download with custom output directory
python ov_infer.py \
    --image_path layout.jpg \
    --output_dir ./output
```

### Using Local Model File

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
    --threshold 0.5 \
    --precision fp32 \
    --cache_dir ./models_cache
```

### Using Different Model Precisions

```bash
# Use FP16 model for faster inference
python ov_infer.py \
    --image_path layout.jpg \
    --precision fp16 \
    --device GPU

# Use combined FP32 model (merged output nodes)
python ov_infer.py \
    --image_path layout.jpg \
    --precision combined_fp32
```

#### Combine batch size and boxes node for PP-DocLayoutV2
In PP-DocLayoutV2, its output includes two nodes, one is batch size and the other one is output boxes.

* Output Nodes:
  ```bash
  Output 0: fetch_name_0, shape: [?,8], type: <Type: 'float32'>
  Output 1: fetch_name_1, shape: [?], type: <Type: 'int32_t'>
  ```

To handle output easily, you can use `combine_bs_and_boxes_node.py` to combine batch size and boxes node as single output nodes and model will be saved as `<model_name>_combined.xml`.

* Usage
  ```bash
  python combine_bs_and_boxes_node.py \
      --model_path pp_doclayoutv2.xml \
  ```


* Output Nodes after combine batch size and boxes node:
  ```bash
  Output 0: Concat.254, shape: [?,300,8], type: <Type: 'float32'>
  ```


## 📖 Parameters

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `--model_path` | str | ❌ | `None` | OpenVINO IR model path (.xml file). If not specified or `None`, automatically downloads from ModelScope. **Note:** If a specific `.xml` file path is provided, the `--precision` parameter will be ignored |
| `--image_path` | str | ✅ | - | Input image path |
| `--output_dir` | str | ❌ | `./output_ov` | Output directory |
| `--device` | str | ❌ | `GPU` | Inference device: `CPU`, `GPU`, `NPU`, `AUTO` |
| `--threshold` | float | ❌ | `0.5` | Detection confidence threshold |
| `--precision` | str | ❌ | `fp32` | Model precision: `fp16`, `fp32`, `combined_fp16`, `combined_fp32`. **Note:** Only effective when `--model_path` is not specified (auto-download) or points to a directory. If `--model_path` points to a specific `.xml` file, this parameter will be ignored |
| `--cache_dir` | str | ❌ | `None` | ModelScope model cache directory. Uses default cache directory if not specified |

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

# Using local model file
result = paddle_ov_doclayout(
    model_path="pp_doclayoutv2.xml",
    image_path="layout.jpg",
    output_dir="./output",
    device="GPU",
    threshold=0.5
)

# Auto-download model (set model_path to None)
result = paddle_ov_doclayout(
    model_path=None,  # Automatically downloads from ModelScope
    image_path="layout.jpg",
    output_dir="./output",
    device="GPU",
    threshold=0.5,
    precision="fp16",  # Use FP16 model
    cache_dir="./models_cache"  # Optional: specify cache directory
)

print(f"Detected {len(result.boxes)} layout elements")
for box in result.boxes:
    print(f"{box['label']}: {box['score']:.3f} at {box['coordinate']}")
```

## 📄 License

This project is based on PaddleOCR's PP-DocLayoutV2 model. Please follow the corresponding open source license.

## 📧 Contact

For questions or suggestions, please submit an Issue or Pull Request.
