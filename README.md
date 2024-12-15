# JetRacer-Pro-AI-Kit
# https://www.waveshare.com/wiki/JetRacer_Pro_AI_Kit#Basic_motion

1. Download the JetRacer image และแตกไฟล์ให้เรียบร้อยโดยโหลดจากที่นี้ https://drive.google.com/file/d/1ZBdqrwhW2n1uN8rughF7Puw98o76kUcH/view
2. เสียบ SD Card กับคอมพิวเตอร์
3. ใช้ Etcher เขียน image  จากเสร็จ
4. นำ SD Card ไปเสียบที่ Jetson และเปิดเครื่อง
5. นำสาย Micro USB มาเสียบและใช้ Putty เข้าผ่าน Serial 
6. เชื่อมต่อไวไฟ โดยเรียกดูไวไฟที่ Active ด้วยคำสั่ง 
    
    sudo nmcli device wifi list

7. เชื่อมต่อด้วยคำสั่ง

    sudo nmcli device wifi connect <ssid_name> password <password>

8. รีสตาร์ท และถอดสาย USB ออกสังเกตุที่ OLED จะแสดง IP หากสามารถเชื่อมต่อไวไฟได้ 
9. ใช้ Putty login เข้าที่ IP ของ Jetson [user : jetson / pass : jetson]สั่ง 
        
    sudo apt update & sudo apt upgrade 

***หากพบ error Errors were encountered while processing:
 nvidia-l4t-bootloader
 nvidia-l4t-xusb-firmware
 nvidia-l4t-initrd
 
 ให้ทำตามนี้ 

    sudo mv /var/lib/dpkg/info/ /var/lib/dpkg/backup/
    sudo mkdir /var/lib/dpkg/info/

    sudo apt-get update
    sudo apt-get -f install

    sudo mv /var/lib/dpkg/info/* /var/lib/dpkg/backup/

    sudo rm -rf /var/lib/dpkg/info
    sudo mv /var/lib/dpkg/backup/ /var/lib/dpkg/info/

แล้วใช้คำสั่ง  
    
    sudo apt upgrade  
    
อีกครั้ง 
10. อัพเดทไฟล์

cd jetracer
git checkout ws/pro
sudo python3 setup.py install
sudo reboot

11. กำหนด Power mode ด้วยคำสั่ง 

    sudo nvpmodel -m1  
    
    ตรวจสอบด้วยคำสั่ง 
    
    sudo nvpmodel -q 
    
    ที่ OLED จะแสดงข้อความ 
    
    MODE: MAXN หรือ 5W

12. เข้าเว็บ http://<jetson_ip_address>:8888 password : jetson 
13. เชื่อมต่อ usb ของ gamepad เข้ากับคอมพิวเตอร์ และเปิดไปที่ basic_motion.ipynb

    ก่อนสั่งรันโค๊ดให้เปิดสวิตช์ของมอเตอร์ที่ฐานรถก่อน
        ![image](https://github.com/user-attachments/assets/161d147d-19b2-428f-a5a5-7ee656660b83)


    จากนั้นรันโปรแกรมได้เลยจะเป็นการดึงค่าสถานะของรถมาให้ดู 

16. เปิดไฟล์ teleoperation.ipynb กำหนดค่า index= ให้ตรงกับที่แสดงในเว็บ https://hardwaretester.com/gamepad 
    กำหนดค่าชุดแรกตามนี้ 

        from jetracer.nvidia_racecar import NvidiaRacecar
        import traitlets
        car = NvidiaRacecar()
        car.throttle_gain = 0.2

    ชุดถัดมา

        car.steering_offset=0.2
        car.steering = 0
    
    ชุดบังคับรถ
    
        left_link = traitlets.dlink((controller.axes[0], 'value'), (car, 'steering'), transform=lambda x: x)
        right_link = traitlets.dlink((controller.axes[3], 'value'), (car, 'throttle'), transform=lambda x: -x)  

****ตอนนี้ต้องสามารถบัลคับให้รถเคลื่อนที่ได้แล้ว *****
