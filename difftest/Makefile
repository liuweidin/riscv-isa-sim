CC  = clang
CXX = clang++

REPO_PATH ?= ..
GUEST_ISA = riscv64
BUILD_DIR = ./build
NAME = $(GUEST_ISA)-spike-so
BINARY = $(BUILD_DIR)/$(NAME)

SPIKE = $(REPO_BUILD_PATH)/spike
REPO_BUILD_PATH = $(REPO_PATH)/build
REPO_MAKEFILE = $(REPO_BUILD_PATH)/Makefile

CONFIGURE_FLAGS =         \
	-q --enable-dirty     \
	--with-boost=no       \
	--with-boost-asio=no  \
	--with-boost-regex=no \
	CC=$(CC) CXX=$(CXX)   \
	CXXFLAGS="-g0"

INC_DEPENDENCIES = fesvr riscv disasm customext fdt softfloat spike_main spike_dasm build
INC_PATH  = -I$(REPO_PATH) $(addprefix -I$(REPO_PATH)/, $(INC_DEPENDENCIES))
INC_PATH += -I$(NEMU_HOME)/include
LIB_DEPENDENCIES = libspike_main.a libriscv.a libdisasm.a libsoftfloat.a libfesvr.a libfdt.a
INC_LIBS  = $(addprefix $(REPO_PATH)/build/, $(LIB_DEPENDENCIES))
SRCS = $(shell find . -name '*.cc')
HEADERS = $(shell find ./include -name '*.h')
CXXFLAGS = -O3 --std=c++17 -shared -fPIC

ifneq ($(DEBUG),)
CONFIGURE_FLAGS += --enable-commitlog
CXXFLAGS += -DDIFFTEST_LOG_FILE=\"$(NOOP_HOME)/build/spike.log\"
endif

$(BUILD_DIR):
	mkdir -p $(BUILD_DIR)

$(REPO_MAKEFILE): $(REPO_PATH)/configure
	mkdir -p $(@D)
	cd $(@D) && $(abspath $(REPO_PATH))/configure $(CONFIGURE_FLAGS)

$(SPIKE): $(REPO_MAKEFILE)
	$(MAKE) -s -C $(^D)

$(BINARY): $(BUILD_DIR) $(SPIKE) $(SRCS) $(HEADERS)
	$(CXX) $(CXXFLAGS) $(INC_PATH) $(SRCS) $(INC_LIBS) -o $@

clean:
	rm -rf $(BUILD_DIR) $(REPO_BUILD_PATH)

.DEFAULT_GOAL = $(BINARY)

.PHONY: clean $(SPIKE)
