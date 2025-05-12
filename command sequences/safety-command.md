# Safety Command Sequence 
```mermaid
sequenceDiagram
    Actor Operator
    participant MCC/GS
    box FINCH
	participant RF
    participant OBC
    participant ADCS
    participant PAY
	end

    OBC ->> PAY: Cmd PAY_Safety ()
    PAY -->> OBC: Fbk PAY_Safety <br> (Status = CmdRecieved)
    PAY ->> PAY: Safety_PowerOff
  
    OBC ->> ADCS: Cmd ADCS_Safety ()
    ADCS -->> OBC: Fbk ADCS_Safety <br> (Status = CmdRecieved)
    ADCS ->> ADCS: Safety_<?>
    note over ADCS: missing information on <br> this internal function (Q)


    OBC ->> OBC: Safety_<?>
    note over OBC: missing information on <br> this internal function (Q)
    note over OBC: This internal function loops until <br> RF interrupts with a command to OBC
	
	
	alt Operator comamnds "Safety" mode exit
        Operator ->> MCC/GS: Cmd ExitSafety ()
		MCC/GS ->> RF: TransmitCmd ExitSafety ()
		RF ->> OBC: TransmitCmd ExitSafety ()

        OBC ->> PAY: Cmd PAY_ExitSafety ()
        PAY ->> PAY: ExitSafety_PowerUp
        PAY -->> OBC: Fbk PAY_ExitSafety <br> (Status = CmdRecieved)
  
        OBC ->> ADCS: Cmd ADCS_ExitSafety ()
        ADCS ->> ADCS: ExitSafety_<?>
        note over ADCS: missing information on <br> this internal function (Q)
        ADCS -->> OBC: Fbk ADCS_ExitSafety <br> (Status = CmdRecieved)

		note over OBC: Are there any internal OBC commands to <br> execute before transitioning to Idle Mode? (Q)
		
		par
			OBC -->> RF: Fbk ExitSafety <br> (Status = IdleMode)
			RF -->> MCC/GS: TransmitFbk ExitSafety <br> (Status = IdleMode)
			MCC/GS -->> Operator: TransmitFbk ExitSafety <br> (Status = IdleMode)
		and
			rect rgb(54,74,63)
				Operator -> PAY: Ref <br/> Enter "Idle" Sequence <br/> parameters = 
			end
		end
	else else
		Operator ->> MCC/GS: Cmd CheckError ()
		MCC/GS ->> RF: TransmitCmd CheckError ()
		RF ->> OBC: TransmitCmd CheckError ()
		OBC ->> OBC: GetErrorInformation
		OBC -->> RF: Fbk CheckError <br> (Fbk = ErrorInformation)
		RF -->> MCC/GS: TransmitFbk CheckError <br> (Fbk = ErrorInformation)
		MCC/GS -->> Operator: TransmitFbk CheckError <br> (Fbk = ErrorInformation)
	end
```
