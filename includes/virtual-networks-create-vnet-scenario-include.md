## <a name="scenario"></a>Сценарий
Чтобы лучше проиллюстрировать процесс создания виртуальной сети и подсетей, в этом документе будет использоваться представленный ниже сценарий.

![Сценарий виртуальной сети](./media/virtual-networks-create-vnet-scenario-include/vnet-scenario.png)

В этом сценарии вы создадите виртуальную сеть с именем **TestVNet** и зарезервированным блоком CIDR **192.168.0.0./16**. Ваша виртуальная сеть будет содержать следующие подсети. 

* **FrontEnd** с блоком **192.168.1.0/24** в качестве блока CIDR.
* **BackEnd** с блоком **192.168.2.0/24** в качестве блока CIDR.



<!--HONumber=Nov16_HO2-->


