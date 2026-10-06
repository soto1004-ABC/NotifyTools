TARGET		:= 3guohero_switch
BUILD		:= build
SOURCES		:= .
INCLUDES	:= .

ARCH	:=	-march=armv8-a -mtune=cortex-a57 -mtp=soft -fPIE
CFLAGS	:=	-g -Wall -O2 -ffunction-sections $(ARCH)
CFLAGS	+=	-I$(PORTLIBS)/include/SDL2 -I$(PORTLIBS)/include -I$(LIBNX)/include -D__SWITCH__
CXXFLAGS	:= $(CFLAGS) -fno-rtti -fno-exceptions
ASFLAGS	:=	-g $(ARCH)
LDFLAGS	=	-specs=$(LIBNX)/switch.specs -g $(ARCH) -Wl,-Map,$(notdir $*.map)
LIBS	:=	-lSDL2_ttf -lSDL2_image -lSDL2_mixer -lSDL2 -lfreetype -lpng -lbz2 -lz -lnx -lm

ifneq ($(BUILD),$(notdir $(CURDIR)))
export OUTPUT	:=	$(CURDIR)/$(TARGET)
export VPATH	:=	$(foreach dir,$(SOURCES),$(CURDIR)/$(dir))
export BUILDDIR	:=	$(CURDIR)/$(BUILD)
export DEPSDIR	:=	$(BUILDDIR)
CFILES		:=	$(foreach dir,$(SOURCES),$(notdir $(wildcard $(dir)/*.c)))
CPPFILES	:=	$(foreach dir,$(SOURCES),$(notdir $(wildcard $(dir)/*.cpp)))
export LD	:=	$(CXX)
export OFILES	:=	$(CPPFILES:.cpp=.o) $(CFILES:.c=.o)
export INCLUDE	:=	$(foreach dir,$(INCLUDES),-I$(CURDIR)/$(dir)) -I$(CURDIR)/$(BUILD)

.PHONY: $(BUILD) clean all

all: $(BUILD)

$(BUILD):
	@[ -d $@ ] || mkdir -p $@
	@$(MAKE) --no-print-directory -C $(BUILD) -f $(CURDIR)/Makefile

clean:
	@echo clean ...
	@rm -fr $(BUILD) $(TARGET).pfs0 $(TARGET).nro $(TARGET).nacp $(TARGET).elf

else
DEPENDS	:=	$(OFILES:.o=.d)
$(OUTPUT).nro	:	$(OUTPUT).elf
$(OUTPUT).elf	:	$(OFILES)
-include $(DEPENDS)
endif
