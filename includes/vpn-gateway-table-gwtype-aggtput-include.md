| | **Пропускная способность VPN-шлюза (1)** | **Макс. количество IPsec- туннелей для VPN-шлюза (2)** | **Пропускная способность шлюза для ExpressRoute** | **Сосуществование VPN-шлюза и ExpressRoute**|
|--- |----------------------------|-----------------------------------|-------------------------------------|-----------------------------------------|
| **SKU "Базовый"** | 100 Мбит/с | 10 | 500 Мбит/с | Нет |
| **SKU "Стандартный"** | 100 Мбит/с | 10 | 1000 Мбит/с | Да |
| **SKU "Высокая производительность" (3)** | 200 Мбит/с | 30 | 2000 Мбит/с | Да |

- (1) Пропускная способность VPN — это приблизительное значение, основанное на измерениях между несколькими виртуальными сетями в одном регионе Azure. Невозможно предугадать, какую пропускную способность вы получите при распределенных подключениях через Интернет, но ее значение следует считать максимальным.
- (2) количество туннелей относится к VPN на основе маршрутов ниже. VPN на основе политик может поддерживать только один VPN-туннель типа "сеть — сеть".
- (3) SKU "Высокая производительность" не поддерживается для типа VPN на основе политик.

<!---HONumber=AcomDC_0727_2016-->