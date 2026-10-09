Handling sim_sampler_configs.zip:

Download and extract sim_sampler_configs.zip into the same parent directory as SwitchSim and mwperf. The folder names when extracted may be incorrect. Rename them accordingly. The resulting directory tree should be:

    experiment_directory/
    ├── SwitchSim_v20.1_rel/
    ├── mwperf_C32_CP09_rel/
    └── configs/
      ├── c1/
      ├── c2/
      └── c3/

  Configuration files are located at, for example:

    configs/c1/cfg/s01/n004_r060.json



Running SwitchSim:
  SwitchSim requires Python 3.12. Download and extract the simulator and configuration archives into the same directory:

  SwitchSim_v20.1_rel/
  SwitchSim_v20.1_schema4_configs/

  From a Windows terminal, run:

  
    cd SwitchSim_v20.1_rel
    python -m pip install -r requirements.txt
    python -m voqsim.cli "../configs/c1/cfg/s01/n004_r060.json" --output-dir "../results/s01_n004_r060" --no-slot-trace

  From a Linux terminal, run:
  
    cd SwitchSim_v20.1_rel
    python3 -m pip install -r requirements.txt
    python3 -m voqsim.cli ../configs/c1/cfg/s01/n004_r060.json --output-dir ../results/s01_n004_r060 --no-slot-trace

  This runs the 4 port full crossbar with system load 0.6 and reports the standard metrics used, leave the slot trace off it will create multiple GB large output files.


Running mwperf (sampler program):
  mwperf must be built and tested before it can run, mwperf only runs on Linux distributions and has only been tested on Ubuntu:


    cd mwperf_C32_CP09_rel

    MWPERF_BUILD_JOBS="$(nproc)" \
      bash sampler/run_integration_validation.sh


  Locate the executable produced by the validation build:
  
    MWPERF_BIN="$(
    find "${MWPERF_WORK_ROOT:-$HOME/mwperf-framework-runs}" \
    -type f -path '*/build-release-on/mwperf' -executable \
    -printf '%T@ %p\n' |
    sort -nr |
    head -n 1 |
    cut -d' ' -f2-
    )"
  
    test -x "$MWPERF_BIN"

  Run one example configuration:

    CONFIG_ROOT="$(realpath ../configs)"
    OUTPUT_ROOT="$(realpath -m ../mwperf-results)"
    mkdir -p "$OUTPUT_ROOT"

    "$MWPERF_BIN" \
      --model "$CONFIG_ROOT/c1/cfg/s01/n004_r060.json" \
      --target q_i000_o000 \
      --output "$OUTPUT_ROOT/c1_s01_n004_r060_q_i000_o000.json" \
      --workers 1 \
      --timeout-seconds 3600 \
      --result-version 3

  
  For the config schema used (schema-4), --target selects the built-in raw-observation (no response curve prefix) profile with warmup 0, horizon 400, 16 replications, population 64, and K=4, 8, and 16 projections. 
  The result-v3 JSON contains both the sampler telemetry and framework reconstruction. Other experiments can be run by changing the model path and target queue identifier. This example once again runs the 4 port full crossbar with system load 0.6.



sim_sampler_results_spreadsheet, and sampler_results had to be split into .7z archives due to filesize constraints. To combine them move all parts into the same directory, right click the first .7z archive (e.g., sampler_results.7z.001 and sim_sampler_results_spreadsheet.7z.001 ) and select 'Extract Here' or 'Extract to folder_name'.

sim_results, and sampler_comparisons can be downloaded and extracted as normal zip files.
