# Python3 OpenCV3
Modify from https://github.com/JarveeLee/SynthText_Chinese_version

# code
The github has the limititation of the file size.So I upload the code in [BaiduYun](https://pan.baidu.com/s/17DF1GnLCT-I0dXJERQE4vA), password:de7l

There is still something wrong with the code.When I use some backgroud pictures,the matlab IDE will be killed.I am trying to figure it out.If you occur the same problem ,please contact me,thanks~

# Steps
I have tested in Ubuntu. Here is my configuration:
  ```
  Python 3.6.4 |Anaconda
  numpy (1.11.0)  
  Matlab2014a       // vim ~/.bashrc
                      # Add an "mrun" alias for running matlab in the terminal.
                      alias mrun="matlab -nodesktop -nosplash -logfile `date +%Y_%m_%d-%H_%M_%S`.log -r"  
  ```

Firstly:

  Add your own pictures in `/prep_script/my_img/img_dir`
  
  Download DCNF-FCSP: https://bitbucket.org/fayao/dcnf-fcsp/get/f66628a4a991.zip in the `prep_scripts` folder，unzip it and rename with `fayao-dcnf-new`.
   
  Then put `run_ucn.m`、`floodFill.py`、`predict_depth.m` 、`Multiscale Combinatorial Grouping`: https://github.com/jponttuset/mcg/archive/master.zip in `pre_scripts/fayao-dcnf-new/demo`.
  
  Download [MatConvNet](http://www.vlfeat.org/matconvnet/download/matconvnet-1.0-beta25.tar.gz),rename with `matconvnet`,put it into `pre_scripts/fayao-dcnf-new/libs`


Secondly:

  Compute the pictures' depth and segment to make the pictues more realistic.

  ```
  cd prep_scripts/fayao-dcnf-new/demo

  mrun run_ucn                             //   prep_script/my_img/ucm.mat
  python floodFill.py                      //   prep_script/my_img/seg.h5

  cd ../libs/matconvnet/
  matlab -nodesktop
        >> addpath matlab/
        >> mex -setup
        >> cd matlab/
        >> vl_compilenn
        >> exit
  cd ../../demo/
  mrun predict_depth	                     //   prep_script/my_img/depth.h5   

  cd ../../my_img/
  python add_more_data.py                   //  prep_script/my_img/dset.h5

  cp dset.h5 ../../data/
  ```

Thirdly:

  Generate the picture.
  
  ```
  cd ../../ 
  python gen.py --viz
  ```

More details can refer to the following chapters.


# SynthText from Ankush

Modify from https://github.com/ankush-me/SynthText.git to generate chinese character 

My OS is Ubuntu opencv2.4 But I am not sure whether it can run on other OS

I changed some func,just run gen.py will be OK,in gen.py I change the depth prediction map with gray map for generating char on cartoon image , for natural img you need to change back to depth map ,other gen**.py contains similar code with different path I do for myself...

0,Before running this code make sure your OS support unicode for chinese.. which as well cost me hours....Added chinese may not make sense because in English words are saperated by blank meanwhile in chinese words are saperated by meaning. 

1,In synthGen I added a function called is_chinese(char ) to or with is_english to cal num of valid chars.

2,Updated the .tff char style files and the path.txt,then 

3,some utf-8 decoded and encoded for chinese char ....Ah I forgot the details....

4,So you can add more pic into the dataset and check with issue under the anthor to fix mistakes......

5,If you want to add more img , firstly you need to compute the segmentation and depth prediction by the 2 matlab code and 1 python code provided by author, and then use the add_more_data.py to generate a new big dset.h5 , containing all of imgs and their seg and depth, then rerun gen.py to see its performance.

These are some samples I do.

** Synthetic Scene-Text Image Samples**
![Synthetic Scene-Text Samples](Figure_1-1.png "Synthetic Samples")

![Synthetic Scene-Text Samples](Figure_1-2.png "Synthetic Samples")

![Synthetic Scene-Text Samples](Figure_1.png "Synthetic Samples")

Code for generating synthetic text images as described in ["Synthetic Data for Text Localisation in Natural Images", Ankush Gupta, Andrea Vedaldi, Andrew Zisserman, CVPR 2016](http://www.robots.ox.ac.uk/~vgg/data/scenetext/).


** Synthetic Scene-Text Image Samples**
![Synthetic Scene-Text Samples](samples.png "Synthetic Samples")

The library is written in Python. The main dependencies are:

```
pygame, opencv (cv2), PIL (Image), numpy, matplotlib, h5py, scipy
```

### Generating samples

```
python gen.py --viz
```

This will download a data file (~56M) to the `data` directory. This data file includes:

  - **dset.h5**: This is a sample h5 file which contains a set of 5 images along with their depth and segmentation information. Note, this is just given as an example; you are encouraged to add more images (along with their depth and segmentation information) to this database for your own use.
  - **data/fonts**: three sample fonts (add more fonts to this folder and then update `fonts/fontlist.txt` with their paths).
  - **data/newsgroup**: Text-source (from the News Group dataset). This can be subsituted with any text file. Look inside `text_utils.py` to see how the text inside this file is used by the renderer.
  - **data/models/colors_new.cp**: Color-model (foreground/background text color model), learnt from the IIIT-5K word dataset.
  - **data/models**: Other cPickle files (**char\_freq.cp**: frequency of each character in the text dataset; **font\_px2pt.cp**: conversion from pt to px for various fonts: If you add a new font, make sure that the corresponding model is present in this file, if not you can add it by adapting `invert_font_size.py`).

This script will generate random scene-text image samples and store them in an h5 file in `results/SynthText.h5`. If the `--viz` option is specified, the generated output will be visualized as the script is being run; omit the `--viz` option to turn-off the visualizations. If you want to visualize the results stored in  `results/SynthText.h5` later, run:

```
python visualize_results.py
```
### Pre-generated Dataset
A dataset with approximately 800000 synthetic scene-text images generated with this code can be found [here](http://www.robots.ox.ac.uk/~vgg/data/scenetext/).

### [update] Adding New Images
Segmentation and depth-maps are required to use new images as background. Sample scripts for obtaining these are available [here](https://github.com/ankush-me/SynthText/tree/master/prep_scripts).

* `predict_depth.m` MATLAB script to regress a depth mask for a given RGB image; uses the network of [Liu etal.](https://bitbucket.org/fayao/dcnf-fcsp/) However, more recent works (e.g., [this](https://github.com/iro-cp/FCRN-DepthPrediction)) might give better results.
* `run_ucm.m` and `floodFill.py` for getting segmentation masks using [gPb-UCM](https://github.com/jponttuset/mcg).

For an explanation of the fields in `dset.h5` (e.g.: `seg`,`area`,`label`), please check this [comment](https://github.com/ankush-me/SynthText/issues/5#issuecomment-274490044).

### Further Information
Please refer to the paper for more information, or contact me (email address in the paper).

