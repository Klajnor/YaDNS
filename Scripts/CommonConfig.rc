:local ScriptName "Config"

:do {
	/system script add name="$ScriptName" policy=read,write source=""
	:put "Script $ScriptName created" 
} on-error = {
	:put "Script $ScriptName already exists" 
}	

/system script set "$ScriptName" source="test"