## Setup
Install ipex-llm[xpu], then install Bigcode with `pip install -e .`

### Configure accelerate
Before running the benchmark, configure the `accelerate` package. 
```
accelerate config
```

The following configuration works for MTL system with iGPU.
```
--------In which compute environment are you running?
This machine
--------Which type of machine are you using?
No distributed training
Do you want to run your training on CPU only (even if a GPU / Apple Silicon device is available)? [yes/NO]:no
Do you want to use XPU plugin to speed up training on XPU? [yes/NO]:yes
Do you wish to optimize your script with torch dynamo?[yes/NO]:no
Do you want to use DeepSpeed? [yes/NO]: no
What GPU(s) (by id) should be used for training on this machine as a comma-seperated list? [all]:all
--------Do you wish to use FP16 or BF16 (mixed precision)?
no
```

## Usage
To utilize ipex-llm, launch script with args `--modeltype ipex-llm`.
Specify ipex-llm-supported precision with `--precision`.
`cpu-embedding` defaults to `false`.
If using load-low-bit API with prequantized models, set `--precision` to `llb`

### Sample script
Generate code and evaluate
```
accelerate launch main.py --model </path/to/model> --modeltype ipex-llm --max_length_generation 512 --tasks humaneval --do_sample False --n_samples 1 --batch_size 1 --precision sym_int4 --allow_code_execution --save_generations --save_generations_path <generations_file.json> --metric_output_path <evaluation_file.json>
```

Generate and save code to file
```
accelerate launch main.py --model </path/to/model> --modeltype ipex-llm --max_length_generation 512 --tasks humaneval --do_sample False --n_samples 1 --batch_size 1 --precision sym_int4 --generation_only --save_generations --save_generations_path <generations_file.json>
```

Generate code using Load-low-bit API and save to file
```
accelerate launch main.py --model </path/to/model-sym_int4> --modeltype ipex-llm --max_length_generation 512 --tasks humaneval --do_sample False --n_samples 1 --batch_size 1 --precision llb --generation_only --save_generations --save_generations_path <generations_file.json>
```

## Notes
- ds1000 test has been disabled to allow code generation to run on Windows system.
- On Windows, only code generation is supported. Evaluation of the generated code (saved to json file) can be done separately on a Linux system.