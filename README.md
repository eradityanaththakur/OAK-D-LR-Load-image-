# OAK-D-LR-Load-image-
you've got this cool OAK-D camera, right? To make it do anything, you need a special tool called the DepthAI library. Think of it as the instruction manual for your camera.
1. The "Pipeline" - Your Camera's Blueprint
Imagine you're building a LEGO spaceship. You need a blueprint, right? For your OAK-D camera, that blueprint is called a Pipeline.

What it is: It's like a plan that tells the camera what to do (e.g., "capture RGB video," "capture stereo images").

How to start one: You always begin with this line in your code:

pipeline = depthai.Pipeline()

2. OAK-D Camera's Eyes - RGB and Stereo
Your OAK-D camera isn't just one camera; it has three "eyes":

RGB Camera (Color): This is your main, high-quality color camera, like the one on your phone. It's great for detailed, colorful pictures and videos.

Example: On some OAK-D cameras, it can be 12 MP (super clear!) and record around 60 frames per second (fps).

Stereo Mono Pair (Gray): These are two separate cameras (left and right) that see in black and white (mono). They work together to figure out how far away objects are, kind of like how your two eyes help you see depth.

Example: They might record at 480P (lower quality than RGB) but can be super fast, like 120 fps!

3. Telling the Camera What to Do (Nodes)
To use these "eyes," you create "nodes" in your pipeline:

For the RGB camera:

camRgb = pipeline.create(dai.node.ColorCamera)

For the Left Mono camera:

monoLeft = pipeline.create(dai.node.MonoCamera)
monoLeft.setBoardSocket(dai.CameraBoardSocket.LEFT) # Tells it it's the left one

For the Right Mono camera:

monoRight = pipeline.create(dai.node.MonoCamera)
monoRight.setBoardSocket(dai.CameraBoardSocket.RIGHT) # Tells it it's the right one

4. Getting the Images OUT (XLinkOut)
Once the camera captures an image, you need a way to get it from the camera to your computer. That's where XLinkOut comes in!

What it does: It's like a conveyor belt that takes the image data from the camera and sends it to your computer.

Naming the stream: You give each output a name (like "rgb", "left", "right") so you know which conveyor belt is carrying which image.

xoutRgb = pipeline.create(dai.node.XLinkOut)
xoutRgb.setStreamName("rgb")

Linking: You connect the camera node to the XLinkOut node, telling the camera to send its preview output to the input of your XLinkOut:

camRgb.preview.link(xoutRgb.input)

5. Reading the Images on Your Computer (Queues)
Now that the images are on their way to your computer via XLinkOut, you need to grab them!

The "Queue": Think of a queue as a waiting line for images. When an image arrives from the camera, it waits in this line until you're ready to pick it up.

# Connect to device and start pipeline
with dai.Device(pipeline) as device:
    # Get the "rgb" queue
    qRgb = device.getOutputQueue(name="rgb", maxSize=4, blocking=False)

    while True:
        # Grab the next image from the queue!
        inRgb = qRgb.get()

        # Convert it to a format OpenCV can understand
        rgb_frame = inRgb.getCvFrame()

        # Then you can show it using OpenCV (like cv2_imshow in Colab)
        # cv2_imshow("rgb", rgb_frame)

qRgb.get(): This command literally "gets" the next image that's waiting in the "rgb" queue.

inRgb.getCvFrame(): This takes the image data you just got and turns it into a format that OpenCV (a popular image processing library) can easily work with.

And that's the basic idea! You create a plan (pipeline), tell the camera what to capture (nodes), set up a way to get the images out (XLinkOut), and then grab them from a waiting line (queue) on your computer.
