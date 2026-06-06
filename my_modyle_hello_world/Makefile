MODULE_NAME := hello
obj-m := $(MODULE_NAME).o

PWD := $(shell pwd)
KERNEL_DIR ?= /lib/modules/$(shell uname -r)/build

.PHONY: build run remove install uninstall format check clean

build:
	$(MAKE) -C $(KERNEL_DIR) M=$(PWD) modules

run:
	insmod $(PWD)/$(MODULE_NAME).ko

remove:
	rmmod $(MODULE_NAME).ko

install:
	@echo "Установка модуля $(MODULE_NAME)..."
	sudo mkdir -p /lib/modules/$(shell uname -r)/extra/$(MODULE_NAME)
	sudo cp $(MODULE_NAME).ko /lib/modules/$(shell uname -r)/extra/$(MODULE_NAME)/
# 	cp $(MODULE_NAME).conf /etc/modprobe.d/
	sudo depmod -a
	# Добавляем в автозагрузку (создаём конфиг)
	echo "$(MODULE_NAME)" | sudo tee /etc/modules-load.d/$(MODULE_NAME).conf
	# Загружаем модуль сейчас
	sudo modprobe $(MODULE_NAME)
	@echo "Модуль установлен и настроен на автозагрузку"
	@echo "Проверка: lsmod | grep $(MODULE_NAME)"

uninstall:
	@echo "Удаление модуля $(MODULE_NAME)..."
	sudo modprobe -r $(MODULE_NAME) 2>/dev/null || true
	sudo rm -f /lib/modules/$(shell uname -r)/extra/$(MODULE_NAME)/$(MODULE_NAME).ko
	sudo rm -rf /lib/modules/$(shell uname -r)/extra/$(MODULE_NAME) 2>/dev/null || true
	sudo rm -f /etc/modules-load.d/$(MODULE_NAME).conf
# 	rm /etc/modprobe.d/$(MODULE_NAME).conf
# 	# Также удаляем из /etc/modules, если добавляли туда
# 	sudo sed -i '/^$(MODULE_NAME)$$/d' /etc/modules 2>/dev/null || true
	sudo depmod -a
	@echo "Модуль удалён"

format:
	clang-format -i *.c

check:
	./check.sh

clean:
	$(MAKE) -C $(KERNEL_DIR) M=$(PWD) clean


