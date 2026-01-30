# Downlinking Command Sequence
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

    OBC ->> OBC: enter_mode("downlinking")

    OBC ->> ADCS: cmd_adcs_mode("finepointing", orient_info, curr_time, TLE)
    ADCS ->> ADCS: execute("finepointing")
    ADCS -->> OBC: fbk_adcs_execute("finepointing")

    OBC ->> RF: cmd_prepare_downlink()
    RF ->> RF: prepare_downlink
    RF -->> OBC: fbk_prepare_downlink()

    alt Telemetry Downlink
        OBC ->> OBC: get_telemetry_data()
    else Image Downlink

        OBC ->> PAY: cmd_get_image_data()
        PAY ->> PAY: get_image_data()
        PAY -->> OBC: fbk_get_image_data()
    end

    OBC ->> RF: cmd_send_data(data)
    RF -) MCC/GS: transmit_data(data)

    OBC ->> OBC: enter_mode("idle")

	end

```
