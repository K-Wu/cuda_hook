# CUDA Hook
Hooked CUDA-related dynamic libraries by using automated code generation tools. Based on this, you can easily obtain the CUDA API called by the CUDA program, and you can also hijack the CUDA API to insert custom logic.

It implements an ingenious tool to automatically generate code that hooks the CUDA api with CUDA native header files, and is extremely practical and extensible.

At present, the hooking of dynamic libraries such as cuda driver, nvml, cuda runtime, cudnn, cublas, cublasLt, cufft, nvtx, nvrtc, curand, cusparse, cusolver, nvjpeg and nvblas has been completed, and it can also be easily extended to the hooking of other cuda dynamic libraries.

# Support Dynamic Libraries
- CUDA Driver: libcuda.so
- NVML: libnvidia-ml.so
- CUDA Runtime: libcudart.so
- CUDNN: libcudnn.so
- CUBLAS: libcublas.so
- CUBLASLT: libcublasLt.so
- CUFFT: libcufft.so
- NVTX: libnvToolsExt.so
- NVRTC: libnvrtc.so
- CURAND: libcurand.so
- CUSPARSE: libcusparse.so
- CUSOLVER: libcusolver.so
- NVJPEG: libnvjpeg.so
- NVBLAS: libnvblas.so

# Compile
## Environment
- OS: Linux
- Cmake Version: >= 3.8
- GCC Version: >= 4.8
- CUDA Version: 11.4 (best)
- CUDA Driver Version: 470.129.06 (best)
- CUDNN Version: 7.6.5 (best)

## Clone
```
git clone https://github.com/Bruce-Lee-LY/cuda_hook.git
```

## Build
```
cd cuda_hook
./build.sh -t Release -s ON -b OFF
./build.sh -t Debug -s OFF -b ON
```

# Run Sample
```
./run_sample.sh
```

# Tools
## Code Generate
Use CUDA native header files to automatically generate code that hooks CUDA API.
```
cd tools/code_generate
./code_generate.sh
```

# Update to 11.6
The original repo uses CUDA 11.4, and we need to update it to CUDA 11.6. The following is a summary of the update process.
## Updating the raw headers as input to the code_generate python tool.
Copy the corresponding headers from the CUDA 11.6 installation to the include folder.
## Modifications to the generated code
Some changes need to be made according to the include headers in the original repo. The authors changed the code based on CUDA raw headers in order to generate shared libraries in the same way as the original CUDA.
### cublas.h, cublas_v2.h, cublasXt.h
 the following two modifications needs to be made to cublas.h before code generate
 // (1) add __half
 (2) add content in the bottom from cublas_api.h and cublasXt.h
 disabling functions with nvbfloat16 arguments
### cusparse.h, cusparse_v2.h
 the following two modifications have been made
 (1) delete CUSPARSE_DEPRECATED and CUSPARSE_DEPRECATED_ENUM
 // (2) add __half
 merge cusparse_v2.h to cusparse.h 
### cuda_runtime_api.h
todo 
 the following two modifications have been made
 (1) delete __dv()
 (2) add functions in the bottom from host_runtime.h and device_functions.h
## manual creation of _subset.h headers
Stuff not in the generated code needs to be manually added to the _subset.h headers, such as enum types, structs, typedef, macros, etc.