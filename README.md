# Point-DPT
The **only** documented instance of an open-source pointer software that utilizes the position of the user's index finger to ascertain the specific location on the screen they are pointing towards, without requiring external depth cameras or sensors -- showed off with a program which focuses a pointed finger for individuals with tremor's in hands to be-able to reliably point at objects


## How our Pipeline Works
### Tracking the Finger
By utilizing the Mediapipe Python package, we can track the movement of a hand and obtain the following data:
![image](https://user-images.githubusercontent.com/72413722/236637614-cba2e540-579e-45f0-a3cf-101ff3a53bf0.png)
With the aid of the index finger's tip location and body location, we establish a direction vector that allows us to ascertain the direction in which the finger is pointing.
### Creating a Depth Map
We use [Intel's dpt-hybrid-midas model](https://huggingface.co/Intel/dpt-hybrid-midas) to generate an estimated depth map for the current frame, and employ this information to determine the Z coordinate of the direction vector -- the large intel dpt model has harsher impacts to net performance, but leads to a more accurate depth readings.
### Determining Position of Finger Point
To determine the pointing direction of the finger, we employ the direction vector and use a ray marching technique. Starting from the tip of the index finger (nail), we iteratively apply the direction vector until one of the following conditions is met (though they have been simplified and modified for improved accuracy by filtering out invalid distances and erroneous data):
1. The x/y position of the shot-out ray is outside the visible frame bounds, resulting in a failure to draw.
2. The z location of the ray is behind the xy location of the depth image, as depicted in the image scenario below.
3. Neither of the above conditions (1,2) are met, and we proceed to add the X, Y, and Z direction vectors to our current ray position.
![image](https://user-images.githubusercontent.com/67922228/236638892-2f4be85d-e4e6-4f4f-ab78-f00ebf738d94.png)

### Recommended Hardware (for real time processing): 
- Jetson Orion Nano (embedded) 
or 
- GeForce RTX 3050-3060 (laptop & desktop)

### Future Use-Cases 
We will assume that performance issues are not a significant concern within reason since camera images can be sent to an off-shore server to be processed for depth, thereby reducing the client overhead of using the AI model (which when profiled found the depth model as the most performance intensive:
- Provides an intuitive and user-friendly interface without the need for special depth reading hardware.
- Has diverse use cases, including aiding in furniture placement using finger gestures and mapping the hand movements of patients with limited hand mobility.
- Enables pointing at objects of interest in situations where verbal communication is not possible, facilitating better text-to-speech tools by allowing users to select specific blocks of text to say out-loud rather than type them.
- Empowers more hardware to run AR applications -- since depth is an essential component to placing & contorting objects in an AR world accurately.
- Medical World Applications are vast: in addition with Parkinson's Disease, many other neurodegenerative diseases such as Lou Gehrig's Disease, Multiple Sclerosis, and Alzheimier's Disease receive therapy and treatment options as patients motor skills' regress, and they struggle to identify objects.
- Combining the software with text reading capable libraries like Tesseract allows a user to point at text, which is identified and read out loud, opening a new path of accessibility for patients that struggle linguistically and visually.

### How To Build:
Clone the repo:</br>
`git clone https://github.com/16-Bit-Dog/point-dpt`</br>
Install requirments by running the following command in the point-dpt directory</br>
`pip install -r requirements.txt`</br>
additionally install for CUDA support (11.7)</br>
`pip3 install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cu117`</br>

### RECOMMENDED METHOD TO USING THE PROGRAM
- To ensure proper depth creation for the index finger and index finger bone, and to map out the entire hand using our hand tracker; it is recommended to face your hand flat and keep all five finger tips visible inside the rendered frame.
- The hand is represented by white lines and red dots to indicate the mapped bone structure, with purple indicating the important parts of the hand, specifically the "pointing finger."
an example image is below:  
![image](https://user-images.githubusercontent.com/67922228/236642694-6238aaa0-9d31-4570-9cec-4dc364f1a560.png)
