## NOTES

It seems that the dataset will hardly be OpenSourced, see[caffemodel for VPGNet #9](https://github.com/SeokjuLee/VPGNet/issues/9) and [DataSet #2](https://github.com/SeokjuLee/VPGNet/issues/2):

> Regarding the dataset, please check Readme file ("Dataset contact" section) for the dataset questions. It turns out that Samsung wants to have an exclusive control who gets the dataset. Good luck!
> The dataset will be available, but currently is being reviewed by Samsung company. They are undergoing a merger and restructuring, which is delaying the work.

And the model the author provided is somewhat strange, with name `name: "VPGNet-noVP"`, yes, **noVP** ...

UPDATE. In issue [the VP part problem #24](https://github.com/SeokjuLee/VPGNet/issues/24), the author provide the VP part of this model.

## Installation

* [Add opencv_imgcodecs in LIBRARIES #2669](https://github.com/BVLC/caffe/pull/2669/files)
    - Uncomment #LINE175 opencv_imgcodecs since we are using opencv3
* [Installation guide](https://github.com/MiaoDX/system_configuration/blob/master/install_caffe.md)

## Codes

Some nice discuss issues:

* [issues/4](https://github.com/SeokjuLee/VPGNet/issues/4)
* [About post-processing for lane visualization #5](https://github.com/SeokjuLee/VPGNet/issues/5)
* [Some questions about lane post-processing #8](https://github.com/SeokjuLee/VPGNet/issues/8)