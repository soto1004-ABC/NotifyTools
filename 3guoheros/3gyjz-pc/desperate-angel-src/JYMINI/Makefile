# --- 스위치 전용 룰 및 컴파일러 불러오기 ---
ifeq ($(strip $(DEVKITPRO)),)
$(error "Please set DEVKITPRO in your environment.")
endif
include $(DEVKITPRO)/libnx/switch_rules

TARGET		:= 3guohero_switch
BUILD		:= build
SOURCES		:= .
INCLUDES	:= .

ARCH	:=	-march=armv8-a -mtune=cortex-a57 -mtp=soft -fPIE
CFLAGS	:=	-g -Wall -O2 -ffunction-sections $(ARCH) -D__SWITCH__
CFLAGS	+=	-I$(PORTLIBS)/include/SDL2 -I$(PORTLIBS)/include -I$(LIBNX)/include
CXXFLAGS	:= $(CFLAGS) -fno-rtti -fno-exceptions
ASFLAGS	:=	-g $(ARCH)

# 링커 플래그 (libnx는 -specs와 -L로만 제어되며 -lnx 플래그는 절대 사용하지 않습니다)
LDFLAGS = -specs=$(DEVKITPRO)/libnx/switch.specs -g $(ARCH) -L$(DEVKITPRO)/portlibs/switch/lib -L$(LIBNX)/lib -Wl,-Map,$(notdir $*.map)

# 순수 라이브러리 목록 (-lnx 완전 제외)
LIBS := -lSDL2_mixer -lSDL2_image -lSDL2_ttf -lSDL2 -lEGL -lGLESv2 -lnx -lfreetype -lharfbuzz -lbz2 -lz -lpng -lstdc++ -lgcc -lc

ifneq ($(BUILD),$(notdir $(CURDIR)))
export OUTPUT	:=	$(CURDIR)/$(TARGET)
export VPATH	:=	$(foreach dir,$(SOURCES),$(CURDIR)/$(dir))
export BUILDDIR	:=	$(CURDIR)/$(BUILD)
export DEPSDIR	:=	$(BUILDDIR)
CFILES		:=	$(foreach dir,$(SOURCES),$(notdir $(wildcard $(dir)/*.c)))
CPPFILES	:=	$(foreach dir,$(SOURCES),$(notdir $(wildcard $(dir)/*.cpp)))
export LD	:=	$(CXX)
export OFILES	:=	$(CPPFILES:.cpp=.o) $(CFILES:.c=.o)
export INCLUDE	:=	$(foreach dir,$(INCLUDES),-I$(CURDIR)/$(dir)) $(foreach dir,$(LIBDIRS),-I$(dir)/include) -I$(CURDIR)/$(BUILD)
export LIBPATHS	:=	$(foreach dir,$(LIBDIRS),-L$(dir)/lib)

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
	@echo linking $(notdir $@)
	@$(LD) $(LDFLAGS) $(OFILES) $(LIBPATHS) $(LIBS) -o $@

%.o: %.c
	@echo $(notdir $<)
	@$(CC) -MMD -MP -MF $*.d $(CFLAGS) $(INCLUDE) -c $< -o $@

%.o: %.cpp
	@echo $(notdir $<)
	@$(CXX) -MMD -MP -MF $*.d $(CXXFLAGS) $(INCLUDE) -c $< -o $@

-include $(DEPENDS)
endif
