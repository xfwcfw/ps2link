EE_KELF  = ps2link.kelf
EE_BIN  = ps2link.bin
EE_ELF  = ps2link.elf
EE_OBJS = ps2link.o cmdHandler.o excepHandler.o exceptions.o
EE_INCS += -I../include

# This is for the sbv patch
SBVLITE = $(PS2SDK)/sbv
EE_INCS += -I$(SBVLITE)/include
EE_LDFLAGS += -L$(SBVLITE)/lib
EE_LIBS += -lpatches -ldebug

# Normal low loading version
LOADADDR  = 0x100000
STACKADDR = 0x1f8000

# This is to builtin the IRXs into ps2link
EE_CFLAGS += -DBUILTIN_IRXS
EE_LDFLAGS += ps2link_irx.o ps2ip_irx.o ps2smap_irx.o ps2dev9_irx.o ioptrap_irx.o poweroff_irx.o iomanX_irx.o

ifeq ($(CACHED_CFG),1)
EE_CFLAGS += -DUSE_CACHED_CFG
endif

# This is to enable the debug mode into ps2link
ifeq ($(DEBUG),1)
EE_CFLAGS += -DDEBUG -g
endif

LDPARAMS := -Wl,--defsym -Wl,_stack_size=0x8000 -Wl,--defsym -Wl,_stack=$(STACKADDR)
EE_LDFLAGS += -Wl,-Ttext -Wl,$(LOADADDR) $(LDPARAMS)

ifeq ($(DEBUG),1)
EE_LDFLAGS += -g
else
EE_LDFLAGS += -s
endif

all: $(EE_KELF)

clean:
	-rm -f $(EE_OBJS) $(EE_ELF) $(EE_BIN) $(EE_KELF)

check: $(EE_BIN)
	@ee-readelf -l $(EE_BIN) | awk ' /LOAD/ { if ((spare = (-($$4 + $$6) + ("'$(STACKADDR)'" - "0x8000"))) <= 0) { printf("PS2Link is too big, %i (0x%x) bytes missing\n", -spare, -spare); exit -1; } else { printf("PS2Link has %i (0x%x) spare bytes\n", spare, spare) } } '

include $(PS2SDK)/Defs.make
include Rules.make
