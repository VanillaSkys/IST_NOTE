> # ** Config Ip Address **
### config ip address
## Example
* interface Serial 0/0
* ip address 172.16.1.1 255.255.255.0
* clock rate 56000
* no shutdown
> # ** Static Route **
### เหมาะกับเส้นทางที่แน่นอน ขนาดเล็ก ไม่ต้องเปลี่ยนแปลง เส้นทางชัดเจน กรณีหลีกเลี่ยงการใช้dynamic    
### สามารถเป็นเส้นทางสำรองให้ dynamic กรณีล่มได้ และควบคุมได้ง่าย
## Example
* router 1 และ router 2 ต่อเข้า router 3 โดยใช้ dynamic route
* router 3 ต่อกับ router 4 โดยใช้ static route  
* > ip route networkที่ต้องการไป subnetmaskของnetworkที่ต้องการไป ipของปลายสาย
* ip route netwrok subnetmask next
    * show ip route
    * show ip route static
    * show ip route network
## Example Script
* basic 
    * ip route 172.16.1.0 255.255.255.0 172.16.2.1
    * ip route 172.16.1.0 255.255.255.0 s0/0
    * ip route 172.16.1.0 255.255.255.0 172.16.2.1 s/0/0
* default route - ip route 0.0.0.0 0.0.0.0 172.16.2.1
* default route จะทำให้ติดต่อกับทุกปลายทางได้ 
> # ** Dynamic Route **
> #### คิดเอง คิดว่า EIGRP ต่อเหมือนกับ RIP แบบเป็นวงๆ ต้องต่อกันหมด ส่วน OSPF สามารถต่อแบบรวมเป็นจุดเดียวได้ เหมือนtree
* > Router rip version 2
    * ### เหมาะกับขนาดเล็ก ไม่ต้อง UPDATE เส้นทางบ่อย
    * > สัญลักษณ์ใน show ip route ตัว R
    * ## Example
        * router rip
        * version 2
        * network networkที่ต้องการติดต่อ wildcard
        * no auto-summary
    * ## Example Script
        * router rip
        * version 2
        * network 172.16.1.0
        * no auto-summary
* > OSPF
    * ### เหมาะกับขนาดกลางถึงใหญ่ เหมาะกับที่ซับซ้อนกว่า
    > #### คิดเอง คิดว่า ใช้ default-information orginate คู่กับ static default route เพื่อออกไปข้างนอก 
    * > สัญลักษณ์ใน show ip route ตัว O
    * ## Example 
        * router ospf 1
        * network networkที่สายที่ต้องการ wildcard area 0
        * > ### default-information originate _ คือคำสั่งทำเป็นdefault routeให้router ข้างๆ _
    * ## Example script
        * router ospf 1
        * network 10.1.1.0 0.0.0.3 area 0
        * network 10.1.1.4 0.0.0.3 area 0
        * network 10.1.1.8 0.0.0.3 area 0
        * default-information originate
* > EIGRP
    * ### เหมาะกับขนาดกลางถึงใหญ่ ประสิทธิ์ภาพสูง ดีกว่าospf แต่ต้องใช้เฉพาะ cisco
    * > สัญลักษณ์ใน show ip route ตัว D
    * ## Example
        * router eigrp idของeigrp
        * eigrp router-id เลขrouterid ** ไม่จำเป็น **
        * network networkที่ต้องการติดต่อ wildcard
        * no auto-summary
* > BGP
    * ### เหมาะกับขนาดใหญ่
    * ## Example
> # ** ACL (acess-list) **
### ไว้ทำเงื่อนไขการเข้าถึง Number Acl จะมี  Standard (1-99) และ Extend (100-199)
> #### คิดเอง คิดว่า IN คือตรวจสอบการรับข้อมูล เหมือนตรวจสอบ ภายนอก ที่จะเข้ามา ภายใน , Internet ที่จะเข้า องกร | OUT คือการตรวจสอบการส่งข้อมูล เหมือนตรวจสอบ ภายใน ที่จะออก ภายนอก, องกร ที่จะออก Internet
* Standard ** _ source IP _ ** 
* Extended ** _ source IP, Dest IP, App, Service _ ** 
* ## Example
    * ## ACL (config)
        * access-list เลข (permit, deny) source_ip wildcard
    * ## Interface ACL วิ่งเข้าrouter เป็น IN วิ่งออกrouter เป็น OUT อยู่ใน interface (config-if)
        * interface (สายของเราที่จะไปทางนั้น)
        * ip access-group เลข (in, out)
> # ** DHCP **
### ไว้แจกIP
* ## Example
    * ip dhcp excluded-address ip_ที่ห้ามแจก
    * ip dhcp pool ชื่อpool 
        * ### _ คือการทำวง _
        * network เลขnetwork  เลขsubnetmask
        * default-router เลขip_default_gateway
        * dns-server เลขip_DNS
        * lease วัน ชั่วโมง นาที 
        * domain-name ชื่อdomain_name
> # ** Switch**
* > Config password
    * ## Example
        * ## ตั้งรหัสผ่าน console กับ  vty คล้ายกัน
            * line con เลข | ถ้าเป็น vty | line vtp เลขเริ่ม เลขจบ
            * password รหัส
            * login
            * exit
        * ## ตั้งรหัสเข้า priviledged
            * enable scret รหัส
* > Switch Port Security
    * ## Static secure MAC Address
        * switchport mode access
        * switchport port-security
        * switchport port-security mac-address MAC-ADD

    * ## Dynamic secure MAC Address
        * switchport mode access
        * switchport port-security
        * switchport port-security mac-address sticky
    * ## Maximum MAC Address
        * switchport port-security maximum MAX
    * ## Violation mode 
        * switchport port-security violation ?
            * protect | Security violation protect mode
            * restrict | Security violation restrict mode
            * shutdown | Security violation shutdown mode
* > ** VLAN **
    * > สร้าง Vlan
        * ## Example
            * vlan vlan_id
            * name ชื่อ
    * > Port Vlan
        * interface interface_id
        * switchport mode access
        * switchport access vlan vlan_id
    * > Thunk Vlan
        * interface interface_id
        * switchport mode thunk
        * switchport thunk allowed vlan vlan_id,vlan_id ### อันนี้คืออนุญาต vlan ไหนบ้าง แต่ไม่จำเป็น
        * swithcport thunk native vlan vlan_id,vlan_id ### อันนี้คือห้าม vlan ไหนบ้าง แต่ไม่จำเป็น
    * > Inter Vlan
        * > Switch Layer 3
            * interface vlan vlan_id
            * ip address ip_address subnetmask
            * > HSRP
                * standby vlan_id ip default_gateway
                * standby vlan_id preempt ### คือให้ทำงาน หากติด
                * standby vlan_id priority priority_number ### ให้ความสำคัญ แต่ไม่ต้องใช้จะดีกว่าไปใช้ STP ดีกว่า
                * no shutdown
        * > Router
            * interface interface_id.vlan_id
            * encapsulation dot1q 10
            * ip address ip_address subnetmask
            * > HSRP
                * standby vlan_id ip default_gateway
                * standby vlan_id preempt ### คือให้ทำงาน หากติด
                * standby vlan_id priority priority_number ### ให้ความสำคัญ แต่ไม่ต้องใช้จะดีกว่าไปใช้ STP ดีกว่า
                * no shutdown

    * > Stanning Tree Protocol (STP)
        ### ป้องกันการLoop เหมือนตั้ง priority ให้มัน ช่วยในตอนทำ HSRP
        * ## Example
            spanning-tree vlan เลข priority เลขpriority
> ** VTP **
* ## Example
    * vtp version 2
    * vtp mode (server,client)
    * vtp domain domain_name
    * vtp password my_password    
> ** NAT **
* > Static (1:1)
    * ## Example
        * ip nat inside source static local_ip global_ip
        * interface interface_id
        * ip nat inside
        * interface interface_id
        * ip nat outside
* > Dynamic (many:many)
    #### คิดเอาเอง ว่าใช้คู่กับ static route
    * ## Example
        * ip nat pool pool_name start_ip end_ip netmask subnetmask
        * access-list access_number permit source_ip
        * ip nat inside source list access_list_number pool pool_name overload ### ถ้าไม่ใส่ overload จะเป็น 1:1 ถ้าใส่จะเป็น many:many
        * interface interface_id
        * ip nat inside
        * interface interface_id
        * ip nat outside
* > Pat (1:many)
    #### คิดเอาเอง ว่าใช้คู่กับ static route
    * ## Example
        * access-list access_number permit source_ip
        * ip nat inside source list access_list_number interface interface_id overload ### ถ้าไม่ใส่ overload จะเป็น 1:1 ถ้าใส่จะเป็น many:many
        * interface interface_id
        * ip nat inside
        * interface interface_id
        * ip nat outside