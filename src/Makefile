# TurboACC Makefile for Alpine Linux
CC = gcc
CFLAGS = -Wall -Wextra -O2 -D_GNU_SOURCE
LDFLAGS = -lssl -lcrypto -lpthread
TARGET = turboacc
KERNEL_MODULES = kernel/fastnat.ko kernel/fullconenat.ko

# 检测内核头文件
KERNEL_VERSION = $(shell uname -r)
KERNEL_HEADERS = /lib/modules/$(KERNEL_VERSION)/build

.PHONY: all kernel userspace install clean

all: userspace

kernel: $(KERNEL_MODULES)

userspace: $(TARGET)

$(TARGET): turboacc.c userspace/flowoffload.c userspace/dnsacc.c
	$(CC) $(CFLAGS) -o $(TARGET) $^ $(LDFLAGS)

kernel/fastnat.ko: kernel/fastnat.c
	$(MAKE) -C $(KERNEL_HEADERS) M=$(PWD)/kernel modules

kernel/fullconenat.ko: kernel/fullconenat.c  
	$(MAKE) -C $(KERNEL_HEADERS) M=$(PWD)/kernel modules

install: all
	install -Dm755 $(TARGET) $(DESTDIR)/usr/bin/$(TARGET)
	install -Dm644 files/turboacc.conf $(DESTDIR)/etc/turboacc/turboacc.conf
	install -Dm755 files/turboacc.initd $(DESTDIR)/etc/init.d/turboacc
	# 内核模块需要手动安装或通过 dkms

clean:
	rm -f $(TARGET)
	$(MAKE) -C $(KERNEL_HEADERS) M=$(PWD)/kernel clean

.PHONY: all kernel userspace install clean
