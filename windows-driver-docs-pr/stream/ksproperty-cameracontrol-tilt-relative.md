---
title: KSPROPERTY\_CAMERACONTROL\_TILT\_RELATIVE
description: The KSPROPERTY\_CAMERACONTROL\_TILT\_RELATIVE property specifies a camera's vertical tilting status.
keywords: ["KSPROPERTY_CAMERACONTROL_TILT_RELATIVE Streaming Media Devices"]
topic_type:
- apiref
api_name:
- KSPROPERTY_CAMERACONTROL_TILT_RELATIVE
api_location:
- Ksmedia.h
api_type:
- HeaderDef
ms.date: 11/28/2017
ms.localizationpriority: medium
---

# KSPROPERTY\_CAMERACONTROL\_TILT\_RELATIVE


The KSPROPERTY\_CAMERACONTROL\_TILT\_RELATIVE property specifies a camera's vertical tilting status.

## <span id="ddk_ksproperty_cameracontrol_tilt_relative_ks"></span><span id="DDK_KSPROPERTY_CAMERACONTROL_TILT_RELATIVE_KS"></span>


### Usage Summary Table

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th>Get</th>
<th>Set</th>
<th>Target</th>
<th>Property Descriptor Type</th>
<th>Property Value Type</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>Yes</p></td>
<td><p>Yes</p></td>
<td><p>Filter or node</p></td>
<td><p><a href="/windows-hardware/drivers/ddi/ksmedia/ns-ksmedia-ksproperty_cameracontrol_s" data-raw-source="[&lt;strong&gt;KSPROPERTY_CAMERACONTROL_S&lt;/strong&gt;](/windows-hardware/drivers/ddi/ksmedia/ns-ksmedia-ksproperty_cameracontrol_s)"><strong>KSPROPERTY_CAMERACONTROL_S</strong></a> or <a href="/windows-hardware/drivers/ddi/ksmedia/ns-ksmedia-ksproperty_cameracontrol_node_s" data-raw-source="[&lt;strong&gt;KSPROPERTY_CAMERACONTROL_NODE_S&lt;/strong&gt;](/windows-hardware/drivers/ddi/ksmedia/ns-ksmedia-ksproperty_cameracontrol_node_s)"><strong>KSPROPERTY_CAMERACONTROL_NODE_S</strong></a></p></td>
<td><p>LONG</p></td>
</tr>
</tbody>
</table>

 

The property value (operation data) is a LONG that specifies a camera's relative tilt setting. The size of the value represents the desired tilt speed; a higher value represents a higher speed.

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th>Value</th>
<th>Description</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td><p>0</p></td>
<td><p>Stop tilting.</p></td>
</tr>
<tr class="even">
<td><p>Positive value</p></td>
<td><p>Start tilting up.</p></td>
</tr>
<tr class="odd">
<td><p>Negative value</p></td>
<td><p>Start tilting down.</p></td>
</tr>
</tbody>
</table>

 

Remarks
-------

The **Value** member of the [**KSPROPERTY\_CAMERACONTROL\_NODE\_S**](/windows-hardware/drivers/ddi/ksmedia/ns-ksmedia-ksproperty_cameracontrol_node_s) structure specifies the relative tilt.

Note that a particular device might support only a certain speed range. To determine the range of speeds supported by the device, an application can issue a KSPROPERTY\_TYPE\_BASICSUPPORT request. You can specify KSPROPERTY\_TYPE\_BASICSUPPORT in the **Flags** member of the [**KSPROPERTY\_ITEM**](/windows-hardware/drivers/ddi/ks/ns-ks-ksproperty_item) structure.

Some devices support only a single tilt speed. In this case, the sign of the **Value** member indicates which direction to tilt.

When making a set request, the client should supply one of the values in the preceding table in the **Value** member of the KSPROPERTY\_CAMERACONTROL\_NODE\_S structure.

When making a get request, the client receives one of the values in the preceding table in the **Value** member of the KSPROPERTY\_CAMERACONTROL\_NODE\_S structure. The value indicates the current tilt status of the camera.

Requirements
------------

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td><p>Version</p></td>
<td><p>Available for Windows Vista and later versions of the Windows operating system.</p></td>
</tr>
<tr class="even">
<td><p>Header</p></td>
<td>Ksmedia.h (include Ksmedia.h)</td>
</tr>
</tbody>
</table>

## See also


[**KSPROPERTY\_CAMERACONTROL\_NODE\_S**](/windows-hardware/drivers/ddi/ksmedia/ns-ksmedia-ksproperty_cameracontrol_node_s)

