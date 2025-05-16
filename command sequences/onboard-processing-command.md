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
	note over OBC: need clarification on storage of <br> images in PAY_MEM, is there one image? <br> multiple with identifiers?(Q)
	OBC ->> OBC: enteredMode "Onboard Processing" <br> (parameters = imageIdentifier)

    OBC ->> OBC: CheckBatteryLevel
    alt Battery Level == Okay
        OBC ->> PAY: Cmd OnboardProcessing <br> (parameters = imageIdentifier)
        PAY ->> PAY: ExecuteOnboardProcessing
        PAY ->> OBC: Fbk OnboardProcessing <br> (status = Success)
      
        par
            OBC -->> RF: TransmitFbk OnboardProcessing <br> (status = Success)
            RF -->> MCC/GS: TransmitFbk OnboardProcessing <br> (status = Success)
            MCC/GS -->> Operator: TransmitFbk OnboardProcessing <br> (status = Success)
        and
            rect rgb(54,74,63)
      	        Operator -> PAY: Ref <br/> Enter "Idle" Sequence
                note over OBC: Need to wait for a close pass before we go into downlinking, default is to go into Idle mode? or case where go directly into downlinking? (Q)
            end
        end
    else Battery Level ==  Low
        rect rgb(54,74,63)
	        Operator -> PAY: Ref <br/> Enter "Safety" Sequence
            note over OBC: should this be safety or idle? relates to Q regarding charging in safety and idle modes (Q)
        end
    end
```
