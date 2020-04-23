# !RAID Tank Debuffs TSU

![!RAID Tank Debuffs TSU](/Pictures/RAID_Tank_Debuffs_TSU.png?raw=true "!RAID Tank Debuffs TSU")

Trigger State Updater (TSU) based auto-cloning raidgroup tank debuff and boss buff -tracker for tanks. It shows pre-defined tank debuffs and boss buffs with stack counter, timer for time remaining and posibility to highlight certain stack count to indicate time for tank swap, all grouped nicely by character and all the groupnames are clearly shown for improved readability.

Everything is pivoting around Player's first debuff icon so use it to position the auragroup properly:
- Unit's name is on the right side of the unit's first icon.
- Unit's other debuffs expand in a row to the left of the unit's previous icon.
- Player's first icon is the "center point" of all the rows always.
- Positions of everything else is calculated relative to Player's first icon.
- Other tanks get their rows sorted below Player's row.
- Bosses get their rows sorted above Player's row.
- Each unit with active debuffs/buffs gets their own row.
- There is no limit on how many different rows there can be.
- Only Player's debuffs are saturated. All the other icons are desaturated.
- These rules apply even if the Player doesn't have any active debuffs, the other units' rows are still grouped and positioned the same way as Player would have active debuffs.

---

## Updating Debuffs and Buffs list

You can find the list of tank debuffs and boss buffs inside the Icon's `On Init`-action. Either add the `spellId`s of the new debuffs/buffs or get updated `On Init`-action and replace the old one with the new one.

When adding new auras, add the tank debuffs to the `aura_env.debuffs`-table and boss buffs to the `aura_env.buffs`-table using following formating:

```lua
[spellId] = true/number,
	-- 'true' to show when aura is found
	-- 'number' to show when aura is found and highlight when stack-count is equal or above the set number
```

---

The default action can be found under the section [Set up "On Init"-action](/!RAID%20Tank%20Debuffs%20TSU.md#set-up-on-init-action) of this file.


## Building

Easiest way is to import [!RAID Tank Debuffs TSU.txt](/!RAID%20Tank%20Debuffs%20TSU.txt), but if you want to, you can easily make your own.


### Create Dynamic Group and Icon-type aura

TSU takes the aura's base settings and creates auto-clones on top of those settings when triggered. For auto-cloning we need to create a Dynamic Group (we will call it `!RAID Tank Debuffs TSU`, but you can name it what ever you want) to hold all the clones. We will create a Icon-type aura (we will call it `!Tank TSU Icons`, but you can name it what ever you want), and place it inside our group.


### Set up "On Init"-action

First we enable the `Custom` `On Init`-action. In the custom `On Init`-action we introduce the table we will be storing all the names of the tanks in the group and also the tables containing all the `spellId`s and stack counts of the debuffs and buffs we want to track. Paste the following code to the `On Init`-action. This is also the place where you later add new `spellId`s for upcoming future raids.


```lua
local aura_env = aura_env
aura_env.tanks = {}
--aura_env.tanks[WeakAuras.me] = true -- Debug
aura_env.debuffs = {
	--[[
		Player DEBUFFS
		--------------
		[spellId] = true/number
			'true' to show when aura is found
			'number' to show when aura is found and highlight when stack-count is equal or above the set number
	]]--
	--[[ Debug
	[25771] = true, -- Forbearance
	[101643] = true, -- Transcendence
	[116847] = true, -- Rushing Jade Wind
	[119085] = 2, -- Chi Torpedo
	]]--

	--[[ T21 - Antorus ]]
	[246220] = true, -- Fel Bombardment, Aim
	--[244536] = true, -- Fel Bombardment, Bombing - BOSS_WHISPER instead of CLEU-event
	[244892] = true, -- Exploit Weakness
	[244016] = true, -- Reality Tear
	[247367] = true, -- Shock Lance
	[247687] = true, -- Sever
	[250255] = true, -- Empowered Shock Lance
	[254919] = true, -- Forging Strike (from N or H)
	[257978] = true, -- Forging Strike (from LFR)
	[243961] = true, -- Misery
	[244899] = true, -- Fiery Strike
	[245518] = true, -- Flashfreeze
	[245990] = true, -- Taeshalach's Reach
	[244291] = true, -- Foe Breaker
	[248499] = true, -- Sweeping Scythe
	[258039] = true, -- Deadly Scythe

	--[[ T22 - Uldir ]]
	[267787] = true, -- Sanitizing Strike
	[265178] = 3, -- Evolving Affliction
	[265237] = 1, -- Shatter
	[274358] = 4, -- Rupturing Blood
	[274693] = 1, -- Essence Shear

	--[[ T23 - Battle for Dazar'alor ]]
	[283573] = true, -- Sacred Blade
	[288151] = 1, -- Tested
	[282037] = 7, -- Rising Flames
	[285671] = 1, -- Crushed
	[285875] = 2, -- Rending Bite
	[284546] = true, -- Depleted Diamond - Diamond of the Unshakeable Protector
	[282444] = 2, -- Lacerating Claws
	[284831] = true, -- Scorching Detonation
	[289858] = true, -- Crushed
	[285195] = true, -- Deathly Withering - DoT, Bwonsamdi tank
	[285213] = true, -- Caress of Death - Healing Immunity, Bwonsamdi tank
	[8286742] = true, -- Necrotic Smash
	[8286646] = 1, -- Gigavolt Charge
	[8284168] = true, -- Shrunk
	[286105] = true, -- Tampering!
	[285000] = true, -- Kelp-Wrapped
	[287993] = 10, -- Chilling Touch
	[287490] = 1, -- Frozen Solid

	--[[ T23 - Crucible of Storms ]]
	[282384] = 3, -- Shear Mind
	[284851] = true, -- Touch of the End

	--[[ T24 - The Eternal Palace ]]
	[300701] = 6, -- Rimefrost
	[300705] = 6, -- Septic Taint
	[296566] = 1, -- Tidefist
	[297397] = 1, -- Briny Bubble
	--[302989] = true, -- Briny Bubble - ???
	[296725] = true, -- Barnacle Bash
	[298156] = 7, -- Desensitizing Sting
	[301830] = 8, -- Pashmar's Touch
	[295480] = true, -- Mind Tether (Main tank)
	[295495] = true, -- Mind Tether (Off tank)
	[298014] = 3, -- Cold Blast
	[302999] = 30, -- Arcane Vulnerability

	--[[ T24 - Ny'alotha, the Waking City ]]
	[306015] = 3, -- Searing Armor (Wrathion, the Black Emperor)
	[307399] = 4, -- Shadow Wounds (Maut)
	[308059] = 6, -- Shadow Shock (The Prophet Skitra)
	[311551] = 1, -- Abyssal Strike (Dark Inquisitor Xanesh)
	[315311] = true, -- Ravage (Aqir Drone / The Hivemind Heroic)
	[307471] = true, -- Crush (Shad'har the Insatiable)
	[307472] = true, -- Dissolve (Shad'har the Insatiable)
	[310277] = 1, -- Volatile Seed (Drest'agath)
	[309961] = 2, -- Eye of N'Zoth (Il'gynoth, Corruption Reborn)
	[307359] = true, -- Despair (Vexiona)
	[310224] = true, -- Annihilation (Void Ascendant / Vexiona)
	[307019] = true, -- Void Corruption (Vexiona)
	[306819] = 1, -- Nullifying Strike (Ra-den the Despoiled)
	[313227] = 1, -- Decaying Wound (Ra-den the Despoiled)
	[315954] = 2, -- Black Scar (Fury of N'Zoth / Carapace of N'Zoth)
	[307044] = 1, -- Nightmare Antibody (Nightmare Antigen / Carapace of N'Zoth)
	[316711] = 1, -- Mindwrack (Psychus, Thought Harvester / N'zoth the Corruptor)
}
aura_env.buffs = {
	--[[
		BOSS BUFFS
		----------
		[spellId] = true/number
			'true' to show when aura is found
			'number' to show when aura is found and highlight when stack-count is equal or above the set number
	]]--
	--[[ Debug
	]]--

	--[[ T23 - Battle for Dazar'alor ]]
	[285945] = 20, -- Hastening Winds (Pa'ku's Aspect / Conclave of the Chosen)
	[289699] = 8, -- Electroshock Amplification (High Tinker Mekkatorque)

	--[[ T24 - The Eternal Palace ]]
	[298428] = 8, -- Feeding Frenzy (Blackwater Behemoth)

	--[[ T24 - Ny'alotha, the Waking City ]]
	[307202] = true, -- Shadow Veil (Tek'ris, Ka'zir / The Hivemind)
	[306942] = true, -- Frenzy (Shad'har the Insatiable P3)
	[312328] = 8, -- Hungry (Shad'har the Insatiable Mythic)
}
```


### Set up Triggers

#### Required for Activation

We need to set up two triggers, one for the auto-cloning and one for the tankswap highlighting. Because we will use the 2nd trigger only for testing if we have aggro, we will set `Required for Activation` to a `Custom Function` and tell the function to pay attention only to the first trigger:

```lua
function(trigger)
	return trigger[1]
end
```

Then we add a Trigger to have total of two (2) Triggers and tell the aura to get the `Dynamic information from Trigger 1`.


#### Trigger 1

For the first Trigger, we will use the TSU and set it up like this:

`Custom -> Trigger State Updater -> Events -> GROUP_ROSTER_UPDATE, CLEU:SPELL_AURA_APPLIED:SPELL_AURA_APPLIED_DOSE:SPELL_AURA_REMOVED_DOSE:SPELL_AURA_REFRESH:SPELL_AURA_REMOVED`

And then we need to create simple `Custom Trigger`-function. All it needs to do is:

1. Detect changes in group roster and updates the tank list if needed.
2. Check on debuff application if the debuff is tracked one and the target is a known tank and determine we need to create new clone or update and old one
3. Check on buff application the buff is tracked one and determine if we need to create new clone or update old one
4. And on any kind of aura removal, check if we had an clone for it and hide it if one is found.

Here is one I made earlier (I know it looks scary and long, but almost half of it is just code to give you preview when you select the group in WeakAuras):

```lua
--[[
	Written by Sanex of Arathor EU
	I got the idea for this from WA I saw made by Reloe-Blackrock EU

	Pimpped up for TSU by Sanex in BfA 8.2
]]--
function(allstates, event, _, eventType, _, _, _, _, _, destGUID, destName, _, _, spellId, _, _, auraType, amount, ...)
	local aura_env = aura_env
	local tanks = aura_env.tanks

	--if event == "ENCOUNTER_START" then
	if event == "GROUP_ROSTER_UPDATE" then
		--wipe(tanks)
		for unit in pairs(tanks) do
			if (not UnitInRaid(unit)) and (not UnitInParty(unit)) then
				tanks[unit] = nil
				print(">>> Removed tank: " .. WA_ClassColorName(unit))
			end
		end

		for unit in WA_IterateGroupMembers() do -- Update table holding the names of the tanks of the raid roster
			local name = UnitName(unit)
			--if UnitIsVisible(unit) and name ~= WeakAuras.me and UnitGroupRolesAssigned(name) == "TANK" then
			if UnitIsVisible(unit) and UnitGroupRolesAssigned(name) == "TANK" then
				if not tanks[name] then
					tanks[name] = true
					print(">>> Found tank: " .. WA_ClassColorName(name))
				end
			else
				if tanks[name] then
					tanks[name] = nil
					print(">>> Removed tank: " .. WA_ClassColorName(name))
				end
			end
		end
	elseif event == "COMBAT_LOG_EVENT_UNFILTERED" then
		local debuffs = aura_env.debuffs
		local buffs = aura_env.buffs

		if eventType == "SPELL_AURA_APPLIED" or eventType == "SPELL_AURA_APPLIED_DOSE" or eventType == "SPELL_AURA_REMOVED_DOSE" or eventType == "SPELL_AURA_REFRESH" then
			--if auraType == "DEBUFF" or auraType == "BUFF" then -- Debug
			if auraType == "DEBUFF" then
				if debuffs[spellId] and tanks[destName] then
				--if (debuffs[spellId] or buffs[spellId]) and destName == WeakAuras.me then -- Debug
					local _, icon, count, _, duration, expirationTime = WA_GetUnitDebuff(destName, spellId)
					--local _, icon, count, _, duration, expirationTime = WA_GetUnitAura(destName, spellId, auraType == "BUFF" and "HELPFUL" or "HARMFUL") -- Debug

					--print("amount/count:", amount, count) -- Debug

					local needWarning = false
					--if (type(debuffs[spellId]) == "number" and debuffs[spellId] > 0) and (count and count > 0 and count >= debuffs[spellId]) then -- Threshold exists and we are at it or over it
					if (debuffs[spellId] ~= true) and (count and count > 0 and count >= debuffs[spellId]) then
						needWarning = true
					end

					if allstates[destGUID .. spellId] then -- Update existing one
						allstates[destGUID .. spellId].show = true
						allstates[destGUID .. spellId].changed = true
						allstates[destGUID .. spellId].duration = duration
						allstates[destGUID .. spellId].expirationTime = expirationTime
						allstates[destGUID .. spellId].stacks = count
						allstates[destGUID .. spellId].warningGlow = needWarning
					else -- Create new
						allstates[destGUID .. spellId] = {
							show = true,
							changed = true,
							progressType = "timed",
							duration = duration,
							expirationTime = expirationTime,
							name = WA_ClassColorName(Ambiguate(destName, "short")),
							icon = icon,
							stacks = count,
							tankName = destName,
							isBoss = false,
							isMe = destName == WeakAuras.me,
							warningGlow = needWarning,
							autoHide = true
						}
					end
				end
			elseif auraType == "BUFF" then
				if buffs[spellId] and ((strsplit("-", destGUID)) == "Creature" or (strsplit("-", destGUID)) == "Vehicle") then -- It's a boss
					for i = 1, 5 do
						local unit = "boss" .. i
						if UnitExists(unit) then
							if UnitGUID(unit) == destGUID then
								local _, icon, count, _, duration, expirationTime = WA_GetUnitBuff(unit, spellId)
								--local _, icon, count, _, duration, expirationTime = WA_GetUnitAura(destName, spellId, auraType == "BUFF" and "HELPFUL" or "HARMFUL")

								local needWarning = false
								--if (buffs[spellId] and buffs[spellId] > 0) and (count and count > 0 and count >= buffs[spellId]) then -- Threshold exists and we are at it or over it
								if (buffs[spellId] ~= true) and (count and count > 0 and count >= buffs[spellId]) then
									needWarning = true
								end

								if allstates[destGUID .. spellId] then -- Update existing one
									allstates[destGUID .. spellId].show = true
									allstates[destGUID .. spellId].changed = true
									allstates[destGUID .. spellId].duration = duration
									allstates[destGUID .. spellId].expirationTime = expirationTime
									allstates[destGUID .. spellId].stacks = count
									allstates[destGUID .. spellId].warningGlow = needWarning
								else -- Create new
									allstates[destGUID .. spellId] = {
										show = true,
										changed = true,
										progressType = "timed",
										duration = duration,
										expirationTime = expirationTime,
										name = WA_ClassColorName(destName),
										icon = icon,
										stacks = count,
										tankName = destName,
										isBoss = true,
										isMe = true,
										warningGlow = needWarning,
										autoHide = true
									}
								end

								break -- Right boss found, no need to iterate anymore...
							end
						end
					end
				end
			end
		elseif eventType == "SPELL_AURA_REMOVED" then
			--if debuffs[spellId] or buffs[spellId] then
				if allstates[destGUID .. spellId] then -- If exists, hide it
					allstates[destGUID .. spellId].show = false
					allstates[destGUID .. spellId].changed = true
				end
			--end
		end
	elseif event == "OPTIONS" and WeakAuras.IsOptionsOpen() then
		local now = GetTime()
		local optionsDuration = 15
		local myName = UnitName("player")
		local offtankName = "Hank the Tank"
		local offtankClass = "PALADIN"
		local bossName = "Hungry Boss"

		local r = fastrandom(15)
		allstates["Me-298156"] = { -- Desensitizing Sting
			show = true,
			changed = true,
			progressType = "timed",
			duration = optionsDuration + r,
			expirationTime = now + optionsDuration + r,
			name = WA_ClassColorName(myName),
			icon = 132274,
			stacks = 5,
			tankName = myName,
			isBoss = false,
			isMe = true,
			warningGlow = false,
			autoHide = true
		}
		r = fastrandom(15)
		allstates["Me-296725"] = { -- Barnacle Bash
			show = true,
			changed = true,
			progressType = "timed",
			duration = optionsDuration + r,
			expirationTime = now + optionsDuration + r,
			name = WA_ClassColorName(myName),
			icon = 1498835,
			stacks = 1,
			tankName = myName,
			isBoss = false,
			isMe = true,
			warningGlow = false,
			autoHide = true
		}
		r = fastrandom(15)
		allstates["Offtank-301830"] = { -- Pashmar's Touch
			show = true,
			changed = true,
			progressType = "timed",
			duration = optionsDuration + r,
			expirationTime = now + optionsDuration + r,
			name = offtankName,
			icon = 1391616,
			stacks = 5,
			tankName = "|c" .. RAID_CLASS_COLORS[offtankClass].colorStr .. offtankName .. "|r",
			isBoss = false,
			isMe = false,
			warningGlow = false,
			autoHide = false
		}
		r = fastrandom(15)
		allstates["Offtank-296566"] = { -- Tidefist
			show = true,
			changed = true,
			progressType = "timed",
			duration = optionsDuration + r,
			expirationTime = now + optionsDuration + r,
			name = offtankName,
			icon = 2032605,
			stacks = 1,
			tankName = "|c" .. RAID_CLASS_COLORS[offtankClass].colorStr .. offtankName .. "|r",
			isBoss = false,
			isMe = false,
			warningGlow = true,
			autoHide = true
		}
		r = fastrandom(15)
		allstates["Offtank-302999"] = { -- Arcane Vulnerability
			show = true,
			changed = true,
			progressType = "timed",
			duration = optionsDuration + r,
			expirationTime = now + optionsDuration + r,
			name = offtankName,
			icon = 236221,
			stacks = 15,
			tankName = "|c" .. RAID_CLASS_COLORS[offtankClass].colorStr .. offtankName .. "|r",
			isBoss = false,
			isMe = false,
			warningGlow = false,
			autoHide = true
		}
		r = fastrandom(15)
		allstates["Boss-298428"] = { -- Feeding Frenzy
			show = true,
			changed = true,
			progressType = "timed",
			duration = optionsDuration + r,
			expirationTime = now + optionsDuration + r,
			name = bossName,
			icon = 463487,
			stacks = 12,
			tankName = bossName,
			isBoss = true,
			isMe = false,
			warningGlow = true,
			autoHide = true
		}
	end

	return true
end
```

Under the `Custom Variables` enter following and we are done with first Trigger:

```lua
{
	expirationTime = true,
	isMe = "bool",
	warningGlow = "bool"
}
```


#### Trigger 2

This trigger is used to track if we have aggro or not, set it up as follows:

`Status -> Threat Situation -> Target`

And make sure you check the checkbox called `Aggro` until it turns into checked checkbox with text `Not Aggro` and we are done with Triggers.


### Set up Display

With Display settings we can leave it pretty much on the default-settings, but I'll do few optional steps changing the `Cooldown Settings` by checking the checkboxes for `Show Cooldown`, `Cooldown Swipe` and `Hide Cooldown Text` and setting the precision to `Dynamic 12.3` under the `Sub Elements` and `Common Text`. For mandatory part of the setting up the Display-section, we create three (3) Text-elements and one (1) Glow-element.

We setup the first Text-element to have huge numbers in the middle of the icon and show the stack count:

- Text: `%s`
- Size: `48`
- Anchor: `CENTER`

For second Text-element we setup smaller number in the bottom right corner showing the time remaining:

- Text: `%p`
- Size: `18`
- Anchor: `BOTTOMRIGHT Outter BOTTOMRIGHT -2, 2`

If you prefer to have huge timer and small stack count, you can swap the `%s` and `%p` around in the first and second Text-elements.

The third Text-element is going to contain the names of the units and the aura will always fill the tank names to the third Text-element no matter what you set the `Text` to show.

- Text: `%n`
- Size: `48`
- Anchor: `LEFT Outter RIGHT 10, 0`

And now we set up the glow we get when we need a tank swap:

- Show Glow: `Unchecked`
- Type: `Pixel Glow`
- Color: `255, 0, 0`
- Lines: `8`
- Frequency: `0.25`
- Length: `10`
- Thickness: `2`
- Offset: `0, 0`

All these settings are just an example, you can use your own imagination if this doesn't fancy you and set the fonts, sizes and positions to what ever you like.


### Set up Condition

Now we need to setup when to actually show the glow and other dynamic styling. You can play with these settings if you want or add more of your own conditions.

Desaturate all except my own debuffs:
```
If Trigger 1
	isMe == False
Then
	Desaturate [x]
```

Set Stack-text color to red if taunt is needed (select the right Text-element if you swapped them around in Display-section):
```
If Trigger 1
	warningGlow == True
Then
	1. Text Color 255, 0, 0
```

Change the remaining time color when there is 3 seconds or less left (select the right Text-element if you swapped them around in Display-section):
```
If Trigger 1
	Remaining Duration <= 3
Then
	2. Text Color 255, 64, 128
```

Show Glow-element if taunt is needed:
```
If All of
If Trigger 1
	warningGlow == True
and Trigger 2
	Active == True
Then
	1. Glow Show Glow [x]
```

And we are almost done, we just have to group and order the cloned icons properly.


### Set up Custom Grow

Currently there isn't anything telling the Dynamic Group how to sort these clones per tank or boss and we can't have that. To fix this we need to go to the Group-settins and change `Grow` to `Custom` and drop in following abomination of a function to the `Custom Grow`-editbox to do the sorting and positioning for us:

```lua
--[[
	Written by Sanex of Arathor EU
	Probably inefficient AF...

	I'm sure there is better and easier way of achieving what I'm tryig to do here.
]]--
function(newPositions, activeRegions)
	local tanks, bosses = {}, {}
	local numTanks, numBosses = 1, 1
	local myCount = 0
	local x, y = 0, 0
	local step = 10

	for i = 1, #activeRegions do
		--[[
			active			bool
			cloneId			cloneId
			id				auraId
			controlPoint	table
			data			table
			region			table
			dataIndex		number
		]]--
		local r = activeRegions[i].region
		local s = r.state
		local iconGroupNamePlate
		for j = #r.subRegions, 1, -1 do

			if r.subRegions[j].type == "subtext" then
				iconGroupNamePlate = r.subRegions[j].text
				break
			end
		end
		if not iconGroupNamePlate then
			print("!!! No iconGroupNamePlate", #r.subRegions)
			newPositions[i] = { x, y }
			return
		end

		if s and s.tankName then
			if s.isBoss then
				if bosses[s.tankName] then
					bosses[s.tankName][1] = bosses[s.tankName][1] + 1
				else
					bosses[s.tankName] = {
						0,
						numBosses
					}
					numBosses = numBosses + 1
				end
				x = (r.width + step) * -bosses[s.tankName][1]
				y = (r.width + step) * bosses[s.tankName][2]

				if x == 0 then
					iconGroupNamePlate:SetText(s.tankName)
				else
					iconGroupNamePlate:SetText("")
				end

			elseif s.tankName ~= WeakAuras.me then
				if tanks[s.tankName] then
					tanks[s.tankName][1] = tanks[s.tankName][1] + 1
				else
					tanks[s.tankName] = {
						0,
						numTanks
					}
					numTanks = numTanks + 1
				end
				x = (r.width + step) * -tanks[s.tankName][1]
				y = (r.width + step) * -tanks[s.tankName][2]

				if x == 0 then
					iconGroupNamePlate:SetText(WA_ClassColorName(s.tankName) ~= "" and WA_ClassColorName(s.tankName) or s.tankName)
				else
					iconGroupNamePlate:SetText("")
				end

			else
				x = (r.width + step) * -myCount
				y = 0
				myCount = myCount + 1

				if x == 0 then
					iconGroupNamePlate:SetText(WA_ClassColorName(s.tankName) or s.tankName)
				else
					iconGroupNamePlate:SetText("")
				end

			end
		end

		--print(">", i, x, y) -- Debug

		newPositions[i] = { x, y }
	end
end
```

What the above function does is, it groups the clones into groups per tank or boss and draws them in rows. The function also sets the first icon to have the unit's name shown on the right side of the icon and grow the other icons to the left of the previous icon. Player's first icon always gets the central pivoting position and bosses' rows are sorted above your row and other tanks rows are sorted below your row.

And that's it, we are all done. If you are not getting any errors, the aura should work and if you want, you can set it up to load only when you are tanking and/or in instance.

---