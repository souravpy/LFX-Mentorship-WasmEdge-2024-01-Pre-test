# LFX-Mentorship-WasmEdge-2024-01-Pre-test
Applying for: 
[LFX Mentorship (Mar-May, 2024): Integrate burn.rs as a new WASI-NN backend #3172](https://github.com/WasmEdge/WasmEdge/issues/3172)
building dependencies following the documentation
## Install rust env on fedora and cmake
```
$ sudo dnf install rust cargo
$ sudo dnf install cmake
```
## Install Wasm Edge
```
curl -sSf https://raw.githubusercontent.com/WasmEdge/WasmEdge/master/utils/install.sh | bash
source $HOME/.wasmedge/env
```
![](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/Pasted%20image.png?raw=true)

## Burn pretest
[Creating a Burn application](https://burn.dev/book/getting-started.html)

initializing burn_app, navigating to its directory
```
cargo new my_burn_app
cd my_burn_app/
```
Adding burn as dependency, there are several backends like ``` wgpu, candle, tch, ndarray ``` we will use the backend specified in the documentation ```wgpu```.
```
cargo add burn --features wgpu
```
![output](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/Screenshot%20from%202024-02-05%2011-40-09.png?raw=true)

Compiling local package
```
cargo build
```
![](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/Pasted%20image%202.png?raw=true)

Writing the code snippet in src/main.rs and replacing the pre-existing code:
```
use burn::tensor::Tensor;
use burn::backend::Wgpu;

// Type alias for the backend to use.
type Backend = Wgpu;

fn main() {
    let device = Default::default();
    // Creation of two tensors, the first with explicit values and the second one with ones, with the same shape as the first
    let tensor_1 = Tensor::<Backend, 2>::from_data([[2., 3.], [4., 5.]], &device);
    let tensor_2 = Tensor::<Backend, 2>::ones_like(&tensor_1);

    // Print the element-wise addition (done with the WGPU backend) of the two tensors.
    println!("{}", tensor_1 + tensor_2);
}
```
Run the app:
```
cargo run
```
![](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/Pasted%20image%201.png?raw=true)

## rustls build and test
We have already installed prerequisites cmake, rust and wasm edge
Cloning Wasm Edge ```hydai/0.13.5_ggml_lts```and navigating to rustls 
```
git clone git@github.com:WasmEdge/WasmEdge.git -b hydai/0.13.5_ggml_lts
cd WasmEdge/plugins/wasmedge_rustls
```
Build rustls plugin
```
cargo build --release
```
![](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/Pasted%20image%203.png?raw=true)

Add the binaries
```
cp libwasmedge_rustls.so  ~/.wasmedge/plugin/
```
## Run Examples
Install wasm target
```rustup target add wasm32-wasi```
### WasmEdge Hyper Demo https client
Cloning the Reqwest Demo repository and navigating to ```client-https``` directory
```
git clone https://github.com/WasmEdge/wasmedge_hyper_demo
cd wasmedge_hyper_demo/client-https
```
Building with ```wasm32-wasi``` target and compiling
```
cargo build --target wasm32-wasi --release
```
![](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/Pasted%20image%208.png?raw=true)
```
wasmedge compile target/wasm32-wasi/release/wasmedge_hyper_client_https.wasm wasmedge_hyper_client_https.wasm
```
![](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/wasm%20hyper%20demo.png?raw=true)

### Wasm reqwest demo https client
Clone and navigate to ```client-https```
```
git clone https://github.com/WasmEdge/wasmedge_hyper_demo
cd wasmedge_hyper_demo/client-https
```
Building and running
```
cargo build --target wasm32-wasi --release
```
![](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/Pasted%20image%205.png?raw=true)
```
wasmedge compile target/wasm32-wasi/release/https.wasm https.wasm
```
![](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/Pasted%20image%207.png?raw=true)
```
wasmedge https.wasm
```
![](https://github.com/souravpy/LFX-Mentorship-WasmEdge-2024-01-Pre-test/blob/main/images/Pasted%20image%206.png?raw=true)
