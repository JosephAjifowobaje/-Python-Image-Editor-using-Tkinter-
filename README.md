# -Python-Image-Editor-using-Tkinter-
import tkinter as tk
from tkinter import filedialog, image_names
from PIL import Image, ImageTk, ImageFilter, ImageEnhance, ImageDraw
from PIL.ImageOps import expand, grayscale
from PIL.ImagePalette import sepia

# Create the root window
root = tk.Tk()
root.title("Enhanced Image Editior")

# Global variables to hold the loaded image and it's displayed version
original_img = None
display_img = None
color_img = None
is_grayscale = False

control_frame = tk.Frame(root)
control_frame.pack(side='left', fill='y')

image_frame = tk.Frame(root)
image_frame.pack(side='right', fill='both', expand=True)

# Function to load and display an image
def load_image():
    global original_img, display_img, color_img, is_grayscale
    file_path = filedialog.askopenfilename()
    if file_path:
        original_img = Image.open(file_path)
        original_img.thumbnail((400, 400))
        color_img =original_img.copy()
        display_img = ImageTk.PhotoImage(original_img)
        img_label.config(image=display_img)
        img_label.image = display_img
        is_grayscale = False

def batch_processs():
    file_path = filedialog.askopenfilenames()
    for path in file_path:
        #'C:\\Users\\DELL\\PycharmProjects\\pythonProject2\\.venv\\Scripts\\photoEditor.py'
        img =Image.open(path)
        # Example: Apply Grayscale to all image in batch
        grayscale = img.convert('L')
        #Save each processed image
        grayscale.save(f'{path}_grayscale.jpg')

#Function to apply blur and filter
def blur_shade(value):
    global original_img, display_img
    if original_img:
        blur_intensity = float(value) * 10
        blurred_img = original_img.copy().filter(ImageFilter.GaussianBlur(blur_intensity))
        display_img = ImageTk.PhotoImage(blurred_img)
        img_label.config(image=display_img)
        img_label.image = display_img
    else:
        print('No image loaded Please load an image first')

# Rotate image function
def rotate_image():
    global original_img, display_img
    if original_img:
        original_img = original_img.rotate(90, expand=True)
        display_img = ImageTk.PhotoImage(original_img)
        img_label.config(image=display_img)
        img_label.image = display_img

def draw_text():
    global original_img, display_img
    if original_img:
        draw = ImageDraw.Draw(original_img)
        draw.text((10,10), 'Kevin De Bruyne', fill='blue')
        display_img = ImageTk.PhotoImage(original_img)
        img_label.config(image=display_img)
        img_label.image = display_img

def sepia_image():
    global original_img, display_img
    if original_img:
        sepia = original_img.convert('RGB')
        pixels = sepia.load()
        for i in range(sepia.width):
            for j in range(sepia.height):
                r, g, b = pixels[i, j]
                tr = int(0.393*r+0.769*g+0.189*b)
                tg = int(0.349*r+0.686*g+0.168*b)
                tb = int(0.272*r+0.534*g+0.131*b)
                pixels[i, j] = (min(tr, 255), min(tg,255), min(tb,255))
        display_img =ImageTk.PhotoImage(sepia)
        img_label.config(image=display_img)
        img_label.image = display_img

def sharpen_image():
    global original_img, display_img
    if original_img:
        sharpened = original_img.filter(ImageFilter.SHARPEN)
        display_img = ImageTk.PhotoImage(sharpened)
        img_label.config(image=display_img)
        img_label.image = display_img

# Flip image function
def flip_image():
    global original_img, display_img
    if original_img:
        original_img = original_img.transpose(Image.FLIP_LEFT_RIGHT)
        display_img = ImageTk.PhotoImage(original_img)
        img_label.config(image=display_img)
        img_label.image = display_img

def adjust_brightness(value):
    global original_img,display_img
    if original_img:
        enhancer = ImageEnhance.Brightness(original_img)
        brightened = enhancer.enhance(float(value))
        display_img = ImageTk.PhotoImage(brightened)
        img_label.config(image=display_img)
        img_label.image = display_img

# Grayscale toggle function
def toggle_grayscale():
    global original_img, display_img, color_img, is_grayscale
    if original_img:

        if not is_grayscale:

            original_img = original_img.convert('L')
            is_grayscale =True
        else:
            original_img = color_img.copy()
            is_grayscale = False

        display_img = ImageTk.PhotoImage(original_img)
        img_label.config(image=display_img)
        img_label.image = display_img
        
# Save image function
def save_image():
    if original_img:
        save_path = filedialog.asksaveasfilename(defaultextension='jpg',
                                                 filetypes=[('JPEG files', '*.jpg'), ('All files', '*.*')])
        if save_path:
            original_img.save(save_path)


# Create and Place the widgets
load_button = tk.Button(control_frame, text="Load Image", command=load_image)
load_button.pack(side='top', fill='both', expand=True)

rotate_button = tk.Button(control_frame, text='Rotate 90', command=rotate_image)
rotate_button.pack(side='top', fill='both', expand=True)

flip_button = tk.Button(control_frame, text='Flip Horizontal', command=flip_image)
flip_button.pack(side='top', fill='both', expand=True)

toggle_grayscale_button = tk.Button(control_frame, text='Toggle Grayscale', command=toggle_grayscale)
toggle_grayscale_button.pack(side='top', fill='both', expand=True)

sepia_image_botton = tk.Button(control_frame, text='Sepia Image', command=sepia_image)
sepia_image_botton.pack(side='top', fill='both', expand=True )

sharpen_image_button= tk.Button(control_frame, text='Sharpen Image', command=sharpen_image)
sharpen_image_button.pack(side='top', fill='both', expand=True)

draw_text_button = tk.Button(control_frame, text='Draw Text', command=draw_text)
draw_text_button.pack(side='top', fill='both', expand=True)

batch_processs_botton = tk.Button(control_frame, text='Batch Process', command=batch_processs)
batch_processs_botton.pack(side='top', fill='both', expand=True)

blur_shade_scale = tk.Scale(control_frame, from_=0.1, to=0.9, resolution=0.1, orient="horizontal", label='Blur Image', command=blur_shade)
blur_shade_scale.pack(side='top', fill='both', expand=True)

brightness_adjuster = tk.Scale(control_frame, from_=0.1, to=0.9, resolution=0.1, orient='horizontal',label='Brightness',command=adjust_brightness)
brightness_adjuster.pack(side='top', fill='both', expand=True)

save_button = tk.Button(control_frame, text='save Image', command=save_image)
save_button.pack(side='top', fill='both', expand=True)



img_label = tk.Label(image_frame)
img_label.pack()

# Run the GUI application
root.mainloop()

