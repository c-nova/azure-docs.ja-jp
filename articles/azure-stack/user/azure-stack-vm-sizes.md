---
title: Azure Stack でサポートされている仮想マシンのサイズ | Microsoft Docs
description: Azure Stack でサポートされている仮想マシンのサイズに関するリファレンスです。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 79d18f938dc51bb7eec62120e8bc6743cb2840c4
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58886473"
---
# <a name="virtual-machine-sizes-supported-in-azure-stack"></a>Azure Stack でサポートされている仮想マシンのサイズ

*適用対象:Azure Stack 統合システムと Azure Stack Development Kit*

この記事では、Azure Stack で使用可能な仮想マシンのサイズの一覧を示します。

Azure Stack のディスク IOPS (Input/Output Operations Per Second) は、ディスクの種類ではなく仮想マシン (VM) サイズの関数です。 つまり、Standard_Fs シリーズの VM では、ディスクの種類として SSD と HDD のどちらを選択した場合でも、1 つの追加データ ディスクの IOPS 制限は 2,300 IOPS です。 課される IOPS 制限は、周囲へのノイズを防ぐための上限 (可能な最大値) です。 特定の VM サイズで得られる IOPS を保証するものではありません。

## <a name="virtual-machine-general-purpose"></a>汎用仮想マシン

汎用 VM サイズは、CPU とメモリのバランスの取れた比率を提供します。 これらはテストと開発、小～中規模のデータベース、および低～中程度のトラフィックの Web サーバーに使用されます。 各データ ディスクは、Basic A シリーズを除き、Premium VM サイズは 2300 IOPS です。 Basic A の場合、データ ディスクのサイズは 500 IOPS です。

### <a name="basic-a"></a>Basic A

> [!NOTE]
> *Basic A* の仮想マシン サイズは、ポータルを介して[仮想マシン スケール セット (VMSS) を作成する](../azure-stack-compute-add-scalesets.md)ものとしては廃止されました。 このサイズの VMSS を作成するには、PowerShell またはテンプレートをご使用ください。

|サイズ - サイズ\名前 |vCPU     |メモリ | 一時ディスクの最大サイズ | OS ディスクの最大スループット:(IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |    
|-----------------|-----|---------|---------|-----|------|-----------|----|
|**A0\Basic_A0**  |1    |768 MB   | 20 GB   |300  | 300  |1 / 1x300  |1   |
|**A1\Basic_A1**  |1    |1.75 GB  | 40 GB   |300  | 300  |2 / 2x300  |1   |
|**A2\Basic_A2**  |2    |3.5 GB   | 60 GB   |300  | 300  |4 / 4x300  |1   |
|**A3\Basic_A3**  |4    |7 GB     | 120 GB  |300  | 300  |8 / 8x300  |1   |
|**A4\Basic_A4**  |8    |14 GB    | 240 GB  |300  | 300  |16 / 16X300 |1   |

### <a name="standard-a"></a>Standard A 
|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |    
|----------------|--|------|----|----|----|-------|---------|
|**Standard_A0** |1 |0.768 |20  |500 |500 |1x500  |1 |
|**Standard_A1** |1 |1.75  |70  |500 |500 |2x500  |1 |
|**Standard_A2** |2 |3.5   |135 |500 |500 |4 x 500  |1 |
|**Standard_A3** |4 |7     |285 |500 |500 |8 x 500  |2 |
|**Standard_A4** |8 |14    |605 |500 |500 |16 x 500 |4 |
|**Standard_A5** |2 |14    |135 |500 |500 |4 x 500  |2 |
|**Standard_A6** |4 |28    |285 |500 |500 |8 x 500  |2 |
|**Standard_A7** |8 |56    |605 |500 |500 |16 x 500 |4 |

### <a name="av2-series"></a>Av2 シリーズ
*Azure Stack バージョン 1804 以降が必要*

|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|-----------------|----|----|-----|-----|------|--------------|---------|
|**Standard_A1_v2**  |1   |2   |10   |500 |1,000  |2 / 2x500   |2 |
|**Standard_A2_v2**  |2   |4   |20   |500 |2000  |4 / 4x500   |2 |
|**Standard_A4v2**   |4   |8   |40   |500 |4000  |8 / 8x500   |4 |
|**Standard_A8_v2**  |8   |16  |80   |500 |8000  |16 / 16x500 |8 |
|**Standard_A2m_v2** |2   |16  |20   |500 |2000  |4 / 4x500   |2 |
|**Standard_A4m_v2** |4   |32  |40   |500 |4000  |8 / 8x500   |4 |
|**Standard_A8m_v2** |8   |64  |80   |500 |8000  |16 / 16x500 |8 |

### <a name="d-series"></a>D シリーズ
|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|----------------|----|----|-----|----|------|------------|---------|
|**Standard_D1** |1   |3.5 |50   |500 |3000  |4 / 4x500   |1 |
|**Standard_D2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |


### <a name="ds-series"></a>DS シリーズ
|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|-----------------|----|----|-----|-----|------|-------------|---------|
|**Standard_DS1** |1   |3.5 |7    |1,000 |4000  |4 / 4x2300   |1 |
|**Standard_DS2** |2   |7   |14   |1,000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3** |4   |14  |28   |1,000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4** |8   |28  |56   |1,000 |32000 |32 / 32x2300 |8 |

### <a name="dv2-series"></a>Dv2 シリーズ
|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|-------------------|----|----|-----|----|------|------------|---------|
|**Standard_D1_v2** |1   |3.5 |50   |500 |3000  |4 / 4x500   |1 |
|**Standard_D2_v2** |2   |7   |100  |500 |6000  |8 / 8x500   |2 |
|**Standard_D3_v2** |4   |14  |200  |500 |12000 |16 / 16x500 |4 |
|**Standard_D4_v2** |8   |28  |400  |500 |24000 |32 / 32x500 |8 |
|**Standard_D5_v2** |16  |56  |800  |500 |48000 |64 / 64x500 |8 |

### <a name="dsv2-series"></a>DSv2 シリーズ
|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|--------------------|----|----|----|-----|------|-------------|---------|
|**Standard_DS1_v2** |1   |3.5 |7   |1,000 |4000  |4 / 4x2300   |1 |
|**Standard_DS2_v2** |2   |7   |14  |1,000 |8000  |8 / 8x2300   |2 |
|**Standard_DS3_v2** |4   |14  |28  |1,000 |16000 |16 / 16x2300 |4 |
|**Standard_DS4_v2** |8   |28  |56  |1,000 |32000 |32 / 32x2300 |8 |
|**Standard_DS5_v2** |16  |56  |112 |1,000 |64000 |64 / 64x2300 |8 |


## <a name="compute-optimized"></a>コンピューティング最適化
### <a name="f-series"></a>F シリーズ
*Azure Stack バージョン 1804 以降が必要*

|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|-----------------|----|----|-----|----|------|------------|---------|
|**Standard_F1**  |1   |2   |16   |500 |3000  |4 / 4x500   |2 |
|**Standard_F2**  |2   |4   |32   |500 |6000  |8 / 8x500   |2 |
|**Standard_F4**  |4   |8   |64   |500 |12000 |16 / 16x500 |4 |
|**Standard_F8**  |8   |16  |128  |500 |24000 |32 / 32x500 |8 |
|**Standard_F16** |16  |32  |256  |500 |48000 |64 / 64x500 |8 |


### <a name="fs-series"></a>Fs シリーズ
*Azure Stack バージョン 1804 以降が必要*  

|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|------------------|----|----|----|-----|------|-------------|---------|
|**Standard_F1s**  |1   |2   |4   |1,000 |4000  |4 / 4x2300   |2 |
|**Standard_F2s**  |2   |4   |8   |1,000 |8000  |8 / 8x2300   |2 |
|**Standard_F4s**  |4   |8   |16  |1,000 |16000 |16 / 16x2300 |4 |
|**Standard_F8s**  |8   |16  |32  |1,000 |32000 |32 / 32x2300 |8 |
|**Standard_F16s** |16  |32  |64  |1,000 |64000 |64 / 64x2300 |8 |


### <a name="fsv2-series"></a>Fsv2 シリーズ
*Azure Stack バージョン 1804 以降が必要* 

|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_F2s_v2**  |2   |4   |16   |1,000 |4000   |4 / 4x2300    |2 |
|**Standard_F4s_v2**  |4   |8   |32   |1,000 |8000   |8 / 8x2300    |4 |
|**Standard_F8s_v2**  |8   |16  |64   |1,000 |16000  |16 / 16x2300  |8 |
|**Standard_F16s_v2** |16  |32  |128  |1,000 |32000  |32 / 32x2300  |8 |
|**Standard_F32s_v2** |32  |64  |256  |1,000 |64000  |32 / 32x2300  |8 |
|**Standard_F64s_v2** |64  |128 |512  |1,000 |128000 |32 / 32x2300  |8 |


## <a name="memory-optimized"></a>メモリ最適化

メモリ最適化済み VM のサイズは、リレーショナル データベース サーバー、中規模から大規模のキャッシュ、インメモリ分析のために設計された、メモリと CPU の高い比率を提供します。

### <a name="mo-d"></a>D シリーズ
|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|------------------|---|----|----|--------|------|------------|---------|
|**Standard_D11**  |2  |14  |100 |500     |6000  |8 / 8x500   |2 |
|**Standard_D12**  |4  |28  |200 |500     |12000 |16 / 16x500 |4 |
|**Standard_D13**  |8  |56  |400 |500     |24000 |32 / 32x500 |8 |
|**Standard_D14**  |16 |112 |800 |500     |48000 |64 / 64x500 |8 |

### <a name="mo-ds"></a>DS シリーズ
|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|-------------------|---|----|----|--------|------|-------------|---------|
|**Standard_DS11**  |2  |14  |28  |1,000    |8000  |8 / 8x2300   |2 |
|**Standard_DS12**  |4  |28  |56  |1,000    |12000 |16 / 16x2300 |4 |
|**Standard_DS13**  |8  |56  |112 |1,000    |32000 |32 / 32x2300 |8 |
|**Standard_DS14**  |16 |112 |224 |1,000    |64000 |64 / 64x2300 |8 |

### <a name="mo-dv2"></a>Dv2 シリーズ
|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|--------------------|----|----|-----|----|-------|-------------|---------|
|**Standard_D11_v2** |2   |14  |100  |500 |6000   |8 / 8x500    |2 |
|**Standard_D12_v2** |4   |28  |200  |500 |12000  |16 / 16x500  |4 |
|**Standard_D13_v2** |8   |56  |400  |500 |24000  |32 / 32x500  |8 |
|**Standard_D14_v2** |16  |112 |800  |500 |48000  |64 / 64x500  |8 |


### <a name="mo-dsv2"></a>DSv2 シリーズ
|Size     |vCPU     |メモリ (GiB) | 一時ストレージ (GiB)  | OS ディスクの最大スループット (IOPS) | 一時ストレージの最大スループット (IOPS) | データ ディスクの最大スループット (IOPS) | 最大 NIC 数 |
|---------------------|----|----|-----|-----|-------|--------------|---------|
|**Standard_DS11_v2** |2   |14  |28   |1,000 |8000   |4 / 4x2300    |2 |
|**Standard_DS12_v2** |4   |28  |56   |1,000 |16000  |8 / 8x2300    |4 |
|**Standard_DS13_v2** |8   |56  |112  |1,000 |32000  |16 / 16x2300  |8 |
|**Standard_DS14_v2** |16  |112 |224  |1,000 |64000  |32 / 32x2300  |8 |


## <a name="next-steps"></a>次の手順

[Azure Stack の仮想マシンに関する考慮事項](azure-stack-vm-considerations.md)
