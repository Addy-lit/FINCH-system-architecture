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

    OBC -) PAY: Cmd PAY_Safety ()
    PAY ->> PAY: Safety_PowerOff
	note over PAY: Anything else that happens here? (Q)
  
    OBC ->> ADCS: Cmd ADCS_Safety ()
    ADCS ->> ADCS: Safety_<?>
	note over ADCS: What happens here? (Q)
    ADCS -->> OBC: Fbk ADCS_Safety <br> (status = CmdRecieved)


    OBC ->> OBC: Safety_<?>
    note over OBC: What happens here? (Q)
	note over OBC: Move into loop or <br> one-time behavior? (Q)

    loop Wait for Ping
        Operator -> PAY: 
    end
	
	note over RF: Failed contact while in safety? (Q)
	alt Operator comamnds "Safety" mode exit
		Operator ->> MCC/GS: Cmd ExitSafety ()
		MCC/GS ->> RF: TransmitCmd ExitSafety ()
		RF ->> OBC: TransmitCmd ExitSafety ()

		note over OBC: We likely need an OBC set of <br> commands here that does something <br> to resolve an issue (N)

        OBC ->> PAY: Cmd PAY_ExitSafety ()
        PAY ->> PAY: ExitSafety_PowerUp
        PAY -->> OBC: Fbk PAY_ExitSafety <br> (status = CmdRecieved)
  
        OBC ->> ADCS: Cmd ADCS_ExitSafety ()
        ADCS ->> ADCS: ExitSafety_<?>
        note over ADCS: What happens here? (Q)
        ADCS -->> OBC: Fbk ADCS_ExitSafety <br> (status = CmdRecieved)

		OBC ->> OBC: SystemHealthCheck
		note over OBC: Need to ensure we have properly <br> prepared to exit the mode, anything <br> else that we need to put here? (Q)

		par
			OBC -->> RF: Fbk ExitSafety <br> (status = Success)
			RF -->> MCC/GS: TransmitFbk ExitSafety <br> (status = Success)
			MCC/GS -->> Operator: TransmitFbk ExitSafety <br> (status = Success)
		and
			rect rgb(54,74,63)
				Operator -> PAY: Enter "Idle" Mode
			end
		end
	else else
		Operator ->> MCC/GS: Cmd CheckError ()
		MCC/GS ->> RF: TransmitCmd CheckError ()
		RF ->> OBC: TransmitCmd CheckError ()
		OBC ->> OBC: GetErrorInformation
		OBC -->> RF: Fbk CheckError <br> (parameter = ErrorInformation)
		RF -->> MCC/GS: TransmitFbk CheckError <br> (parameter = ErrorInformation)
		MCC/GS -->> Operator: TransmitFbk CheckError <br> (parameter = ErrorInformation)
	end

	note over OBC: need to indicate return <br> to loop waiting for ping (N)
```
