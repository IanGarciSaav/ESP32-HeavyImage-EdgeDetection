# Border Detection on ESP32

This project implements an edge detection system that offloads heavy image processing to an ESP32 microcontroller. It splits a large image into smaller chunks, sends them to the ESP32 for processing using the Sobel operator, and reassembles the results into a final image.

## Features

-   **Distributed Processing**: Offloads computational tasks to the ESP32.
-   **Image Splitting**: Automatically divides large images into manageable 80x63 pixel chunks.
-   **Edge Detection**: Implements the Sobel operator on the ESP32 to detect edges in the image.
-   **Serial Communication**: Robust data transfer between PC and ESP32 via UART (Serial).
-   **Image Reconstruction**: Reassembles the processed chunks into a single final image with optional upscaling and sharpening.
-   **Steganography Decryption**: Includes a tool to decrypt hidden messages from images.

## Project Structure

-   `src/ImageSplitter.py`: Splits the input image (`images/imagen_original.png`) into multiple parts stored in `imageDivisions/`.
-   `src/Sender&Receiver.py`: Handles Serial communication. Sends image parts to the ESP32 and saves the processed response in `processedImages/`.
-   `src/border_detection/border_detection.ino`: The firmware for the ESP32. Receives image data, applies the Sobel edge detection algorithm, and returns the result.
-   `src/finalImage.py`: Combines the processed parts from `processedImages/` into `final_image.jpeg`.
-   `src/Desencriptador.py`: A utility script to extract hidden messages from `images/encrypted_logo.png`.

## Requirements

### Hardware
-   ESP32 Microcontroller
-   USB Cable for connection

### Software
-   Python 3.x
-   Arduino IDE (for flashing the ESP32)

### Python Dependencies
Install the required libraries using pip:

```bash
pip install pyserial pillow numpy
```

## Setup & Usage

### 1. Flash the ESP32
1.  Open `src/border_detection/border_detection.ino` in the Arduino IDE.
2.  Connect your ESP32 to the computer.
3.  Select the correct Board and Port in the Arduino IDE.
4.  Upload the sketch.

### 2. Prepare the Image
1.  Place your target image in the `images/` folder and name it `imagen_original.png` (or update `IMAGE_PATH` in `ImageSplitter.py`).
2.  Run the splitter script:
    ```bash
    cd src
    python ImageSplitter.py
    ```
    This will create the image chunks in the `imageDivisions/` folder.

### 3. Process the Images
1.  Ensure the ESP32 is connected.
2.  Check the `SERIAL_PORT` variable in `Sender&Receiver.py` (default is "COM3"). Update it if your ESP32 uses a different port.
3.  Run the sender/receiver script:
    ```bash
    python "Sender&Receiver.py"
    ```
    *Note: The script `Sender&Receiver.py` has special characters, so quote the filename or rename it if necessary.*

### 4. Reconstruct the Final Image
1.  Once all parts are processed, run the reconstruction script:
    ```bash
    python finalImage.py
    ```
    The final edge-detected image will be saved as `final_image.jpeg` in the project root.

## Bonus: Decrypt Hidden Message
To run the steganography decryptor:
```bash
python Desencriptador.py
```
This will read `images/encrypted_logo.png` and output the hidden message to `mensaje_desencriptado.txt`.

## How it Works
1.  **Splitting**: The high-resolution input image is divided into a grid. Each cell is resized to 80x63 pixels to fit within the ESP32's memory constraints.
2.  **Communication**: The PC sends raw byte data of each chunk to the ESP32.
3.  **Processing**: The ESP32 stores the chunk in a buffer and iterates through the pixels, applying the Sobel convolution kernels to calculate gradient magnitude. Pixels with high gradients (edges) are marked white; others are black.
4.  **Reconstruction**: The PC receives the processed chunks and stitches them back together based on their filenames (which contain row/column indices). Post-processing (upscaling, contrast enhancement) is applied to improve visibility.
