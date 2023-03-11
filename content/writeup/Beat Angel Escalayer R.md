---
title: "Beat Angel Escalayer R"
---
## Abstract
Beat Angel Escalayer R (*BEAR*) is a game made by AliceSoft, and its instructions are interprepted using Alicesoft's proprietary virtual machine (VM): [[writeup/System Virtual Machine|System]]. 

*BEAR* has turn-based battles between player-controlled characters and AI opponents. After crafting an exploit for [[writeup/Rance IX|Rance IX]], I then looked at this game due to its AntiCheat features. Specfically, the AntiCheat stores an encrypted copy of data to a buffer, performs a comparison between the copy and new data, and triggers a death flag if there's an inequality, otherwise, the encrypted copy is replaced with the new data. This death flag causes the character to die on the next turn.

I utilized [[writeup/System Virtual Machine#Tools|tools]] designed for the System architecture combined with my previous knowledge to craft a simple exploit for *BEAR*. Largely reminiscent of ACTk for Unity.

## Decompile *ain* file
``` c#
int C_INT::Get()
{
	SH_LOCALDELETE(0);
	C_VALUE_ENCRYPTION cValueEncryption = SH_LOCALCREATE(0, 194);
	int nXorKey = this.GetKey();
	int nValue = cValueEncryption.Decryption(this.m_nCode, nXorKey, this.GetCodePattern());
	int nBackUp = cValueEncryption.Decryption(this.m_nBackUpCode, nXorKey, this.GetBackUpCodePattern());
	if (!system.IsDebugMode())
	{
		if (nValue != nBackUp)
		{
			■チート疑惑設定(true); //Cheat Suspected
			nValue = 0;
		}
		if (this.m_nValue != nValue)
		{
			■チート疑惑設定(true); //Cheat Suspected
		}
		return nValue;
	}
	if (this.m_nValue != nValue)
	{
		this.Set(this.m_nValue);
	}
	return this.m_nValue;
	return 0;
}
```