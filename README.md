<div align="center">
  <h3 align="center">Python LSB Steganography</h3>
  Created as part of university course of cryptology.
</div>

## Implementation

### Encrpyting algorhitm

First, the input data must be processed. It must be read and converted into binary form.

The prefix is determined from the length of the binary data. The prefix has a fixed length of 4 bytes in the implementation. This prefix limits the maximum size of encrypted data, which is (2^32)-1 = 4294967295 bits, or ~ 536.9 MB. This limit is the same regardless of the number of LSBs used in the algorithm. To store such a large file using 2 LSBs, approximately 716 million pixels would be needed, i.e., a square image with a side length of more than 26,000 pixels. Using 1 LSB, such a square would have to have a side length of more than 38,000 pixels. Any expansion consists only of extending the prefix. The total number of pixels required in the image is therefore the sum of the prefix and the binary representation of the file.

The image used to store the information is then loaded and processed. The image must be a bitmap, PNG and BMP formats are supported. The input image can use a color model containing more than three channels (e.g., RGBA). If there are additional channels, these channels are ignored and the bits are inserted back in unchanged form. After processing the input image, the write capacity is calculated. If we try to write more data to the image than the capacity allows, the program terminates and returns an error message. If the data fits into the image, the program continues.

The data masking itself is handled by a loop that is executed for each pixel of the original image. The output of each such loop is a new pixel, which may or may not be changed from the original pixel. This pixel is then stored in a data array containing new pixels. At the end of the algorithm, this array serves as a data source for creating a new image.

After going through all the pixels, an array with changed pixels is available. A new image is assembled from this array, which is then saved to a predetermined path.

## Requirements

To run the program, you need to have Python 3 and the Pillow image processing library installed. The library can be installed using pip.

  ```sh
  pip install -r requirements.txt
  ```

## Unit tests

The program includes unit tests. These test the functionality of individual parts, the whole, and even cases where the program should terminate unsuccessfully. Methods for conversion to and from binary form are tested. Encryption and decryption are also tested, first using 1 LSB and then using 2 LSB. In cases where the program is supposed to terminate unsuccessfully, encryption and decryption are tested using a different number of bits (1-bit encryption and 2-bit decryption, and vice versa) and an attempt to encrypt a file that is too large.

Unit tests can be run as follows:

  ```sh
  python3 -m unittest test_steganography.py
  ```

## Examples

The program operates in `enrypt` and `decrypt` modes. Help for the program and both modes can be displayed using the `-h` or `--help` switch as follows:

  ```sh
  python3 steganography.py --help
  python3 steganography.py encrypt --help
  python3 steganography.py decrypt --help
  ```

### Encrypting

The encryption mode accepts four input parameters. The `--file` parameter specifies the path to the file that will be encrypted into an image. `--image` specifies the path to the image into which the file will be encrypted. `--output` defines the output path of the modified image. The optional parameter `--lsb` allows you to set the number of LSBs used for encryption. If the parameter is not used, encoding is performed to the 2 least significant bits.

For testing purposes, files for use in `file` and `image` are prepared in the `examples` folder.

   ```sh
  python3 steganography.py encrypt --file examples/input_file.txt --image examples/input_image.png --output examples/image_with_secret.png --lsb 2
  ```

### Decrypting

The decryption mode accepts three input parameters. The `--file` parameter specifies the path to the image from which the content will be decrypted. `--output` defines the output path for the newly created file. The optional parameter `--lsb` allows you to set the number of LSBs used for decryption. If the parameter is not used, decoding is performed from the 2 least significant bits.

To test, you must first encrypt the file.

  ```sh
  python3 steganography.py decrypt --file examples/image_with_secret.png --output examples/original_file.txt --lsb 2
  ```
