# zoom_handler
newZoom.py
import tkinter as tk
from PIL import Image, ImageTk
from button_setup import draw_annotations_on_big_canvas

# Starting position for panning
start_x = 0
start_y = 0


scale = 1.0
zoom_counter = 0
initial_image = None  # this will store the original image

def zoom(event, big_canvas,grid_x, grid_y,canvas):
    global tkimage, scale, zoom_counter, initial_image

    if event.delta < 0 or event.num == 5:  # Scroll down
        if scale > 0.5:
            scale *= 0.9
            zoom_counter -= 1
    elif event.delta > 0 or event.num == 4:  # Scroll up
        if scale < 2.0:
            scale *= 1.1
            zoom_counter += 1

    if initial_image is None:
        initial_image = big_canvas.pil_image.copy()  # save the initial image

    if initial_image is None:
        initial_image = big_canvas.pil_image.copy()  # save the initial image

    # Get the initial image dimensions
    width, height = initial_image.size

    # Calculate the new image dimensions
    new_width, new_height = int(width * scale), int(height * scale)

    # Resize the image based on the original one
    #big_canvas.pil_image = initial_image.resize((new_width, new_height), Image.ANTIALIAS)

    # Resize the image based on the current one
    big_canvas.pil_image = big_canvas.pil_image.resize((new_width, new_height), Image.ANTIALIAS)

    # Convert the image object to a Tkinter-compatible photo image
    tkimage = ImageTk.PhotoImage(big_canvas.pil_image)

    # Clear the existing content in the big_canvas
    big_canvas.delete("all")

    # Add the image to the big_canvas
    big_canvas.create_image(0, 0, image=tkimage, anchor="nw")

    # Update the scroll region to the size of the image
    big_canvas.config(scrollregion=big_canvas.bbox("all"))

    big_canvas.scale = scale

    # Get the current zoom scale
    zoom_scale = big_canvas.scale

    # Update the annotations on the big_canvas based on the current zoom scale
    draw_annotations_on_big_canvas(grid_x, grid_y, big_canvas, zoom_scale,canvas)

    return zoom_scale


def start_pan(event):
    global start_x, start_y
    start_x = event.x
    start_y = event.y

def pan(event, big_canvas):
    x, y = (event.x - start_x, event.y - start_y)
    big_canvas.scan_dragto(x, y, gain=1)

