# DigitizedAttendanceSystem-ComputerVision
Taking attendance using facial recognition - part of the COE485 Senior Design project for Term 192

## Developement documentation

This stuff is just info on what happened

### Installing `face_recognition`

If you're getting the error that `dlib` fails to build, [see this issue](https://github.com/ageitgey/face_recognition/issues/802#issuecomment-485256117)

```sh
pip install cmake
pip install Boost
pip --no-cache-dir install face_recognition

cd face_recognition || git clone https://github.com/ageitgey/face_recognition && cd face_recognition
python setup.py install
```

## what I did

I downloaded LFW (faced in the wild dataset)

## Thoughts

| requirements                          | rbpi | podeium PC (web app) | mobile app | nvidia Jetson |
| ------------------------------------- | ---- | -------------------- | ---------- | ------------- |
| automat-activate: no user interaction | yes  | no                   | no         | yes           |
| can override                          | no   | yes                  | yes        | somewhat      |
| show progress/feedback                | no   | yes                  | yes        | somewhat      |
| can infer                             | no   | yes                  | yes        | yes           |

## Justifying decisions

We need something that doesn't need retraining, rather we want something that will extract facial features. The reason is that, if we use a classifier that needs to retrain for each new class/subgroup, it will not scale at all.

We decided to go with edge-computing for now.
Later, we could have 2 products, one that works on the cloud and one that is completely edge based. For the version that is completely on the edge (has no cloud computing), it provides privacy, this is ideal for sensitive environments (such as if the campus was for females in Saudi Arabia). We can capitalize on this and give the customer the option to choose, either a one-time payment and privacy.

The open-source options we found are [OpenFace](https://github.com/TadasBaltrusaitis/OpenFace), and [face_recognition](https://github.com/ageitgey/face_recognition).

