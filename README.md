<div align="center">

<h1> 3D Scene Generation from 2D Images</h1>

<div>

</div>


<tr>
<img  src="https://user-images.githubusercontent.com/33378412/232312245-a1674d48-9726-4bb2-8868-9fbf4a33ca58.png" width="500" height="420">
<img  src="https://user-images.githubusercontent.com/33378412/232312257-3dc680c5-9e94-4392-8334-fe32addadf1f.png" width="500" height="420"> 
<img  src="https://user-images.githubusercontent.com/33378412/232312267-8b9a0016-0452-4029-9e1a-7bb81a6e3310.png" width="500" height="420">
<img  src="https://user-images.githubusercontent.com/33378412/232312341-99cd7140-123f-4e85-8037-3a7eca9f40dd.png" width="500" height="420" >
<img  src="https://user-images.githubusercontent.com/33378412/232312452-a78874f8-6994-4957-8ff9-ae8008f6651e.png" width="500" height="420" >
<img  src="https://user-images.githubusercontent.com/33378412/232312446-fcec1649-a8c4-4b32-b10a-e2ad567865dd.png" width="500" height="420" >


</tr>

</div>

## Installation
You can setup the required environment by the following commands:
```bash
# install python dependencies
conda env create -f environment.yaml
conda activate scenedreamer

# compile third party libraries
export CUDA_VERSION=$(nvcc --version| grep -Po "(\d+\.)+\d+" | head -1)
CURRENT=$(pwd)
for p in correlation channelnorm resample2d bias_act upfirdn2d; do
    cd imaginaire/third_party/${p};
    rm -rf build dist *info;
    python setup.py install;
    cd ${CURRENT};
done

for p in gancraft/voxlib; do
  cd imaginaire/model_utils/${p};
  make all
  cd ${CURRENT};
done

cd gridencoder
python setup.py build_ext --inplace
python -m pip install .
cd ${CURRENT}

# Now, all done!
```

## Inference

### Download Pretrained Models
Please download our checkpoints from [Google Drive](https://drive.google.com/file/d/1IFu1vNrgF1EaRqPizyEgN_5Vt7Fyg0Mj/view?usp=share_link) to run the following inference scripts. You may store the checkpoint at the root directory of this repo:
```
├── ...
└── SceneDreamer
    ├── inference.py
    ├── README.md
    └── scenedreamer_released.pt
```

### Render!
You can run the following command to generate your own 3D world!
```bash
python inference.py --config configs/scenedreamer_inference.yaml --output_dir ./test/ --seed 8888 --checkpoint ./scenedreamer_released.pt
```

The results will be saved under `./test` as the following structures:
```
├── ...
└── test
    └── camera_{:02d} # camera mode for trajectory
        ├── rgb_render # per frame RGB renderings
            ├── 00000.png
            ├── 00001.png
            └── ...
        ├── rgb_render.mp4 # rendered video
        ├── height_map.png # height map
        ├── semantic_map.png # semantic map
        └── style.npy # sampled style code
```


Here is a sampled scene with my default rendering parameters:
<tr>
    <img src="./assets/sample_traj.gif" width="100%"/>
   <img src="https://user-images.githubusercontent.com/33378412/232312533-a7507628-cd6d-410f-9c84-9d95b965dd35.mp4" width="100%"/>
</tr>


    
### Gradio Demo
You can also locally launch  demo with gradio UI by:
```bash
python app_gradio.py
```
