# Onboard Processing Command Sequence
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

	note over OBC: either a command comes through <br> in idle from operator with this parameter, or <br> it comes from imaging mode with this parameter (N)
	note over OBC: need clarification on storage of <br> images in PAY_MEM, is there one image? <br> multiple with identifiers? (Q)
	OBC ->> OBC: enteredMode "Onboard Processing" <br> (parameter = imageIdentifier)

    OBC ->> OBC: CheckBatteryLevel
    alt Battery Level == Okay
        OBC ->> PAY: Cmd OnboardProcessing <br> (parameter = imageIdentifier)
        PAY ->> PAY: ExecuteOnboardProcessing
        PAY ->> OBC: Fbk OnboardProcessing <br> (status = Success)
      
        par
            OBC -->> RF: TransmitFbk OnboardProcessing <br> (status = Success)
            RF -->> MCC/GS: TransmitFbk OnboardProcessing <br> (status = Success)
            MCC/GS -->> Operator: TransmitFbk OnboardProcessing <br> (status = Success)
        and
			alt <something to determine time to enter downlink>
            	rect rgb(54,74,63)
      	        	Operator -> PAY: Ref <br/> Enter "Downlinking" Mode
            	end
			else <else case>
	      		rect rgb(54,74,63)
      	        	Operator -> PAY: Ref <br/> Enter "Idle" Mode
            	end
			end
        end
    else Battery Level ==  Low
        rect rgb(54,74,63)
	        Operator -> PAY: Ref <br/> Enter "Idle" Mode
        end
    end
```
