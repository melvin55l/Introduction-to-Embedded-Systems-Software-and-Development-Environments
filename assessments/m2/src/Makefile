#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# <Put a Description Here>
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <FILE>.o - Builds <FILE>.o object file
#      build    - Builds and links all source files
#      all      - Same as build
#      clean    - removes all generated file
#
# Platform Overrides:
#      PLATFORM - Target platform (HOST, MSP432)
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
PLATFORM = MSP432

# Architectures Specific Flags
LINKER_FILE = ../msp432p401r.lds
CPU = cortex-m4
ARCH = thumb
M_ARCH = armv7e-m
M_FLOAT = hard
FPU = fpv4-sp-d16
SPECS = nosys.specs

GFLAGS = -std=c99 -Wall -Werror -g -O0
TARGET = c1m2


# Compiler Flags and Defines
ifeq ($(PLATFORM),HOST)
	CC = gcc
	CCSIZE = size
	LD = ld
	OBJDUMP = objdump
	LDFLAGS = -Wl,-Map=$(TARGET).map
	CFLAGS  = $(GFLAGS)
	PLATFORM = HOST
	SOURCES = $(HOST_SOURCES)
	INCLUDES = $(HOST_INCLUDES)	
else
	CC = arm-none-eabi-gcc
	CCSIZE = arm-none-eabi-size
	LD = arm-none-eabi-ld
	OBJDUMP = arm-none-eabi-objdump
	LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)
	CFLAGS =  $(GFLAGS) -mcpu=$(CPU) -m$(ARCH) -march=$(M_ARCH) --specs=$(SPECS) -mfpu=$(FPU) -mfloat-abi=$(M_FLOAT) 	
	PLATFORM = MSP432
	SOURCES = $(MSP432_SOURCES)
	INCLUDES = $(MSP432_INCLUDES)
endif


OBJS = $(SOURCES:.c=.o)
PREPRO = $(SOURCES:.c=.i)
ASMS = $(SOURCES:.c=.asm)

# Generate object file to compile
%.o : %.c
	$(CC) -c $^ $(CFLAGS) -D$(PLATFORM) $(INCLUDES)  -o $@

# Generate the preprocessed output of all files
%.i : %.c
	$(CC) -E -D$(PLATFORM) $< $(CFLAGS) $(INCLUDES) -o $@

# Generate assembly output of all files
%.asm : %.c
	$(CC) -S -D$(PLATFORM) $< $(CFLAGS) $(INCLUDES) $(LDFLAGS) -o $@
	$(OBJDUMP) -S -D$(PLATFORM) $< $(CFLAGS) $(INCLUDES) $(LDFLAGS) -o $@


.PHONY: compile-all
compile-all: $(OBJS)

.PHONY: build
build: all

.PHONY: all
all: $(TARGET).out

$(TARGET).out: $(OBJS) 
	$(CC) $^ $(CFLAGS) $(LDFLAGS) -D$(PLATFORM) $(INCLUDES) -o $@ 

.PHONY: clean
clean:
	rm -f $(OBJS) $(TARGET).out $(TARGET).map $(PREPRO) $(ASMS)


