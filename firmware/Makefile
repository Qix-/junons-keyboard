.PHONY: all clean lint clippy fmt doc firmware firmware.run udeps

ifdef DEBUG
CARGO_MODE := debug
else
CARGO_MODE := release
CARGO_FLAGS := --release
endif

ifndef EXTMAC
export EXTMAC=44:45:56
endif

ifndef LEVEL
LEVEL := trace,embassy_net=debug
endif

ifndef PROBE_RUN
PROBE_RUN := probe-run
endif

PROBE_RUN_FLAGS := --log-format "{t} {L} {s} <{f}:{l}>" --verbose --verify

ifdef DEV_IP
CARGO_FLAGS += --features oro-connect-to-ip
endif

ifndef PLINK
PLINK := plink.exe
endif

ifndef COM
COM = COM16
endif

export DEFMT_LOG = $(LEVEL),embassy_hal_internal=warn
export ORO_CONNECT_TO_IP = $(DEV_IP)

# The default here is for my own machine. Change it to refer to
# the serial device being used by your STLink. On Windows/WSL,
# it's COM16, thus /dev/ttyS16.
ifndef LOGDEV
LOGDEV := /dev/ttyS16
endif

all: firmware

clean:
	rm -rf target

fmt:
	env cargo fmt --all

lint:
	env cargo fmt --all -- --check --verbose

clippy:
	env cargo clippy $(CARGO_FLAGS) -Zunstable-options -Zbuild-std=core,compiler_builtins -Zbuild-std-features=compiler-builtins-mem -- -D clippy::all

doc:
	env cargo doc $(CARGO_FLAGS) -Zunstable-options -Zbuild-std=core,compiler_builtins -Zbuild-std-features=compiler-builtins-mem --open

firmware.run: firmware
	$(PROBE_RUN) $(PROBE_RUN_FLAGS) --speed 40000 --chip stm32g473cct3 target/thumbv7em-none-eabihf/$(CARGO_MODE)/junons-keyboard

firmware: target/$(CARGO_MODE)/junons-keyboard.bin
.PHONY: target/thumbv7em-none-eabihf/$(CARGO_MODE)/junons-keyboard
target/thumbv7em-none-eabihf/$(CARGO_MODE)/junons-keyboard:
	env cargo build $(CARGO_FLAGS) -Zunstable-options -Zbuild-std=core,compiler_builtins -Zbuild-std-features=compiler-builtins-mem
target/$(CARGO_MODE)/junons-keyboard.bin: target/thumbv7em-none-eabihf/$(CARGO_MODE)/junons-keyboard
	@mkdir -p "$(dir $@)"
	env arm-none-eabi-objcopy --output binary "$<" "$@"
