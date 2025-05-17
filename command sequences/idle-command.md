# Idle Command Sequence
```mermaid
sequenceDiagram
    actor Operator
    participant MCC/GS
    box FINCH
        participant RF
        participant OBC 
        participant ADCS
        participant PAY
    end

    note over OBC: there needs to be information about <br> what attitude this is, does it have <br> to do with charging? do we need <br> to put other notes for charging? (Q)
    OBC ->> ADCS: Cmd OrientSC <br> (parameter = attitude)
    ADCS ->> ADCS: ExecuteOrient <br> (parameter = attitude)
    ADCS -->> OBC: Fbk OrientSC <br> (status = Success)
    OBC ->> OBC: SystemHealthCheck

    loop Wait for Ping
        OBC ->> OBC: CheckScheduled
        note over OBC: output parameters of schMode and <br> modeParams (N)
        opt Scheduled Mode Change
            rect rgb(54,74,63)
              	Operator -> PAY: Ref <br/> Enter <schMode> Mode <br> (parameter = <modeParams>)
            end
        end
    end

    Operator ->> MCC/GS: Cmd ModeChange <br> (parameter = cmdMode, modeParams, schedule)
    MCC/GS ->> RF: TransmitCmd ModeChange <br> (parameter = cmdMode, modeParams, schedule)
    alt Contact
        RF ->> OBC: TransmitCmd ModeChange <br> (parameter = cmdMode, modeParams, schedule)
        alt Scheduling
            OBC ->> OBC: ScheduleModeChange <br> (parameter = cmdMode, modeParams, schedule)
        else Now
            rect rgb(54,74,63)
                Operator -> PAY: Ref <br/> Enter <cmdMode> Mode <br> (parameter = <modeParams>)
            end
        end
    else Error
        RF -) OBC: Msg Error <br> (parameter = Communication)
        OBC ->> OBC: LogError <br> (parameter = Communication)
        rect rgb(54,74,63)
            Operator -> PAY: Ref <br/> Enter "Safety" Mode
        end
    end

```
