
# `gradio_sbmp_promptable_image`

A webcam-compatible Gradio input image component enabling prompting with the most recently drawn bounding box and multiple points.

## Installation

```bash
pip install gradio_sbmp_promptable_image
```

## Usage

```python

import gradio as gr
from gradio_sbmp_promptable_image import SBMPPromptableImage
import cv2

BLUE = (135, 206, 235)
PINK = (239, 149, 186)

image_examples = [{"image": "images/cat.png", "points": []}]

def get_point_inputs(prompts):
    point_inputs = []
    for prompt in prompts:
        if prompt[5] == 4.0:
            point_inputs.append((prompt[0], prompt[1], prompt[2]))

    return point_inputs

def get_box_inputs(prompts):
    box_inputs = []
    for prompt in prompts:
        if prompt[2] == 2.0 and prompt[5] == 3.0:
            box_inputs.append((prompt[0], prompt[1], prompt[3], prompt[4]))

    return box_inputs

def process_input(input_dict):
    img, points = input_dict['image'], input_dict['points']

    point_inputs = get_point_inputs(points)
    box_inputs = get_box_inputs(points)

    for point in point_inputs:
        x, y = int(point[0]), int(point[1])
        cv2.circle(img, (x, y), 2, (0, 0, 0), thickness=10)
        if point[2] == 1:
            cv2.circle(img, (x, y), 2, BLUE, thickness=8)
        else:
            cv2.circle(img, (x, y), 2, PINK, thickness=8)

    for box in box_inputs:
        x1, y1, x2, y2 = int(box[0]), int(box[1]), int(box[2]), int(box[3])
        cv2.rectangle(img, (x1, y1), (x2, y2), (255, 0, 0), 2)

    return img

demo = gr.Interface(
    process_input,
    SBMPPromptableImage(),
    gr.Image(),
    examples=image_examples,
)

if __name__ == "__main__":
    demo.launch()

```

## Acknowledgements

Special thanks to the creators of [gradio-image-prompter](https://github.com/PhyscalX/gradio-image-prompter/tree/main?tab=readme-ov-file) - this custom component is heavily adapted from their work.


