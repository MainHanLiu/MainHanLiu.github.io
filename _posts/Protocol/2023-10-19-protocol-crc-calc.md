---
title: "循环冗余检验 (CRC) 算法原理"
description: 
date: 2023-10-19 10:00:00 +0800
categories: [Protocol, CRC]
tags: [Protocol, CRC]
pin: false

media_subpath: ""
#
# image:
#   path: 'assets/**/**.jpg'
#   alt: Here is the text upder the image
---

## modbus CRC_16 

### 直接计算法
```c
// 直接计算法计算crc
uint16_t CRC16_Modbus(uint8_t *_pBuf, uint16_t _usLen)
{
	uint16_t CRC_16 = 0xFFFF;
	uint16_t POLYNOMIAL = 0xA001;

	while (_usLen--)
	{
		CRC_16 ^= *_pBuf++;
		for (int i = 0; i < 8; ++i)
		{
			if ((CRC_16 & 0x0001) != 0)
			{
				CRC_16 >>= 1;
				CRC_16 ^= POLYNOMIAL;
			}
			else
			{
				CRC_16 >>= 1;
			}
		}
	}
	return (CRC_16 >> 8 | CRC_16 << 8);
}
```

### 查表法
#### 查表法1
注意：在工程实现中，有时会将高低字节在计算过程中就交换位置，包括生成的CRC_TABLE，也是交换过的。

生成校验表
```c
// CRC_TABLE 高低字节计算
uint8_t CRC_TABLE_Hi[256] = {0};
uint8_t CRC_TABLE_Lo[256] = {0};

void CRC16_TABLE_CALC (void)
{
    uint16_t CRC_TABLE_X = 0x0000;
    uint16_t POLYNOMIAL = 0xA001;
    for (uint16_t i = 0x0000; i <= 0x00FF; i++)
    {
        CRC_TABLE_X = i;
        for (int j = 0; j < 8; j++)
        {
            if ((CRC_TABLE_X & 0x0001) != 0)
            {
                CRC_TABLE_X >>= 1;
                CRC_TABLE_X ^= POLYNOMIAL;
            }
            else
            {
                CRC_TABLE_X >>= 1;
            }
        }
		// 未交换顺序的高低CRC TABLE
        CRC_TABLE_Hi[i] = CRC_TABLE_X >> 8; 
        CRC_TABLE_Lo[i] = CRC_TABLE_X; 
    }
}
```
![输入图片说明](/imgs/protocol-crc-calc/2023-10-19/5IS5K1Va44WRb17J.png)  
![输入图片说明](/imgs/protocol-crc-calc/2023-10-19/2hZVlS9Gz5jSC7FV.png)  

```c
// 查表法1
uint16_t CRC16_Modbus(uint8_t *_pBuf, uint16_t _usLen)
{
	uint8_t ucCRCHi = 0xFF; /* 高CRC字节初始化 */
    uint8_t ucCRCLo = 0xFF; /* 低CRC字节初始化 */
	uint16_t usIndex;  /* CRC循环中的索引 */

    while (_usLen--)
    {
		usIndex = ucCRCLo ^ *_pBuf++; /* 计算CRC */
		ucCRCLo = ucCRCHi ^ CRC_TABLE_Lo[usIndex]; // 右移八位
		ucCRCHi = CRC_TABLE_Hi[usIndex];
    }

    // 交换CRC的高低字节以符合CRC在报文中的顺序
    return ((uint16_t)ucCRCLo << 8 | ucCRCHi);
}
```

####  查表法2  
![输入图片说明](/imgs/protocol-crc-calc/2023-10-19/QpIYjuwKmBfPqdMg.png)  
```c
// 查表法2
uint16_t CRC16_Modbus(const uint8_t *_pBuf, uint16_t _usLen)
{
	

	uint8_t usIndex;
	uint16_t CRC_16 = 0xFFFF;

	while (_usLen--)
	{
		usIndex = *_pBuf++ ^ CRC_16;
		CRC_16 >>= 8;
		CRC_16 ^= CRC_TABLE[usIndex];
	}
	// 交换CRC的高低字节以符合CRC在报文中的顺序
	return ((CRC_16 >> 8) | (CRC_16 << 8));
}
```

## 为什么将CRC附在数据后面再次CRC，结果为0？
第一次计算时，CRC算法将CRC_16的低字节附在前，高字节附在后，即，数据`01 05 00 11 FF 00`计算出的CRC为`3F DC`，在交换后附在数据后面为`01 05 00 11 FF 00 DC 3F`，如果将该值进行第二次CRC算法，计算完`01 05 00 11 FF 00`后，CRC为`3F DC`，下一待计算的字节为`DC`，低字节异或后得出索引为0，计算出CRC为`00 3F`，下一待计算的字节为`3F`，同理计算索引为0，因此最后结果为`0`。

## Reference
- [# 循环冗余检验 (CRC) 算法原理](https://www.cnblogs.com/esestt/archive/2007/08/09/848856.html)  
- [# CRC-16校验原理](https://blog.csdn.net/u012158332/article/details/74356091)  
