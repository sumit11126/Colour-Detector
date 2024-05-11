import cv2
import numpy as np
import imutils
from imutils.video import VideoStream
import pandas as pd


def get_color_name(R, G, B):
    minimum = 10000
    for i in range(len(csv)):
        d = abs(R - int(csv.loc[i, "R"])) + abs(G - int(csv.loc[i, "G"])) + abs(B - int(csv.loc[i, "B"]))
        if d <= minimum:
            minimum = d
            cname = csv.loc[i, "color_name"]
    return cname


index = ["color", "color_name", "hex", "R", "G", "B"]
csv = pd.read_csv('C:\msys64\colors.csv' ,names=index, header=None)

cap = VideoStream(src=0).start();

while True:
    frame = cap.read();
    frame = np.flip(frame, axis=1)
    # hsv_frame = cv2.cvtColor(frame,cv2.COLOR_BGR2HSV)
    frame = imutils.resize(frame, width=640)
    frame = imutils.resize(frame, height=480)

    height, width, _ = frame.shape

    cx = int(width / 2)
    cy = int(height / 2)

    pixel_center = frame[cy, cx]
    hue_value = pixel_center[0]
    s_value = pixel_center[1]
    v_value = pixel_center[2]

    pixel_center_bgr = frame[cy, cx]
    print(pixel_center_bgr)
    b, g, r = int(pixel_center_bgr[0]), int(pixel_center_bgr[1]), int(pixel_center_bgr[2])

    cv2.rectangle(frame, (20, 20), (750, 60), (b, g, r), -1)
    text = get_color_name(r, g, b) + ' R=' + str(r) + ' G=' + str(g) + ' B=' + str(b)
    cv2.putText(frame, text, (50, 50), 2, 0.8, (255, 255, 255), 2, cv2.LINE_AA)
    cv2.circle(frame, (cx, cy), 5, (25, 25, 25), 3)

    if r + g + b >= 600:
        cv2.putText(frame, text, (50, 50), 2, 0.8, (0, 0, 0), 2, cv2.LINE_AA)

    cv2.imshow("Frame", frame)
    key = cv2.waitKey(1)
    if key == 113:
        break

cap.release()
cv2.destroyAllWindows()