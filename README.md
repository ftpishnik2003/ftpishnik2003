import socket
import sys
import os
import platform

def get_wireshark_install_path():
    """Get the Wireshark installation path based on the OS."""
    # Проверяем операционную систему
    if platform.system() == "Windows":
        # Для Windows предполагаем, что Wireshark установлен в "C:\Program Files\Wireshark"
        return "C:\\Program Files\\Wireshark"
    elif platform.system() == "Darwin":
        # Для macOS предполагаем, что Wireshark установлен в стандартном каталоге
        return "/Applications/Wireshark.app/Contents/MacOS"
    elif platform.system() == "Linux":
        # Для Linux можно попробовать найти Wireshark в системных путях
        wireshark_path = os.popen('which wireshark').read().strip()
        if os.path.isfile(wireshark_path):
            return os.path.dirname(wireshark_path)
    # Если не удалось определить путь, возвращаем None
    return None

def check_tshark_availability():
    """Check Tshark install."""
    tshark_path = os.popen('which tshark').read().strip()
    tshark_path = ""
    wireshark_path = get_wireshark_install_path()
    if wireshark_path:
        # Формируем путь к tshark в каталоге установки Wireshark
        tshark_path = os.path.join(wireshark_path, "tshark")

    if not tshark_path:
        os_type = platform.system()
@@ -110,14 +131,21 @@ def choose_interface():
    print("[+] Available interfaces:")
    for idx, iface in enumerate(interfaces, 1):
        print(f"{idx}. {iface}")
        try:
            ip_address = netifaces.ifaddresses(iface)[netifaces.AF_INET][0]['addr']
            print(f"[+] Selected interface: {iface} IP address: {ip_address}")
        except KeyError:
            print("[!] Unable to retrieve IP address for the selected interface.")

    choice = int(input("[+] Enter the number of the interface you want to use: "))
    return interfaces[choice - 1]


def extract_stun_xor_mapped_address(interface):
    """Capture packets and extract the IP address from STUN protocol."""
    print("[+] Capturing traffic, please wait...")

    if platform.system() == "Windows":
        interface = "\\Device\\NPF_"+interface
    cap = pyshark.LiveCapture(interface=interface, display_filter="stun")
    my_ip = get_my_ip()
