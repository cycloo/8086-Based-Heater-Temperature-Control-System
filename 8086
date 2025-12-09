.MODEL SMALL
.STACK 100h

.DATA
; Device A ports
PORTAA     EQU 10h      ; Port A address (device A)
PORTAB     EQU 12h      ; Port B address (device A)
PORTAC     EQU 14h      ; Port C address (device A)
CTRLWORDA  EQU 16h      ; Control word port (device A)

; Device B ports
PORTBA     EQU 08h      ; Port A address (device B)
PORTBB     EQU 0Ah      ; Port B address (device B)
PORTBC     EQU 0Ch      ; Port C address (device B)
CTRLWORDB  EQU 0Eh      ; Control word port (device B)

; Variables (word/byte)
DELAY     DW 0h         ; potentiometer digital value (word)
DSPLYD    DW 0h         ; value to be displayed (word)
HIGHNUM   DB 0h         ; temp high nibble/number (byte)
LOWNUM    DB 0h         ; temp low nibble/number (byte)
SETPOINT  DW 0h         ; set point value (word)
DISPSET   DW 0h         ; displayed set value (word)

.CODE
.STARTUP

; -------------------- Configure Ports --------------------
; Device A: portA=input, portB=output, portC=input-output
MOV AL, 10010000b
OUT CTRLWORDA, AL

; Device B: all outputs
MOV AL, 10000000b
OUT CTRLWORDB, AL

; -------------------- MAIN LOOP --------------------
MAIN PROC
    ; Read potentiometer from PORT A (8-bit), store zero-extended into DELAY (word)
    IN   AL, PORTAA
    MOV  AH, 0
    MOV  DELAY, AX        ; DELAY = AX (word)

    ; Simple delay
    MOV  CX, 00FFh
Delay1:
    LOOP Delay1

    ; Convert/scale DELAY into DSPLYD
    MOV  AX, DELAY
    MOV  BX, 100          ; sensitivity multiplier
    MOV  CX, 10           ; divisor
    MUL  BX               ; AX = AX * BX (DX:AX)
    DIV  CX               ; AX = AX / CX
    ADD  AX, 50           ; add base offset
    MOV  DSPLYD, AX

    ; Short input read & button handling
    MOV  CX, 01D4Ch       ; another delay counter (used later)
    IN   AL, PORTAC       ; read control buttons from Port C
    MOV  BX, 10           ; increment/decrement step value

    ; Check UP button (bit 4 -> 0x10)
    TEST AL, 010h
    JE   CheckDown
    ; UP pressed -> increment DISPSET
    MOV  AX, DISPSET
    ADD  AX, BX
    CMP  AX, 100          ; max setpoint
    JG   MaxTemp
    MOV  DISPSET, AX
    JMP  AfterButtons
MaxTemp:
    SUB  AX, BX
    MOV  DISPSET, AX
    JMP  AfterButtons

CheckDown:
    ; Check DOWN button (bit 5 -> 0x20)
    TEST AL, 020h
    JE   CheckSet
    MOV  AX, DISPSET
    SUB  AX, BX
    CMP  AX, 0            ; min setpoint
    JL   MinTemp
    MOV  DISPSET, AX
    JMP  AfterButtons
MinTemp:
    ADD  AX, BX
    MOV  DISPSET, AX
    JMP  AfterButtons

CheckSet:
    ; Check SET button (bit 6 -> 0x40)
    TEST AL, 040h
    JNE  AfterButtons
    MOV  AX, DISPSET
    MOV  SETPOINT, AX     ; update setpoint
    JMP  AfterButtons

AfterButtons:
    ; WAITING delay loop (uses CX from earlier)
WaitingLoop:
    LOOP WaitingLoop

    ; -------------------- Prepare Display from DSPLYD --------------------
    MOV  AX, DSPLYD
    ; Extract digits (routine follows original approach)
    MOV  BX, 100
    MOV  CX, 10
    XOR  DX, DX           ; clear DX for DIV
    DIV  BX               ; AX = DSPLYD / 100 ; remainder in DX

    MOV  HIGHNUM, AL      ; store quotient low byte as HIGHNUM
    XCHG AL, DL
    XOR  DX, DX
    DIV  CX               ; divide remainder by 10
    MOV  LOWNUM, AL       ; store next digit

    XCHG AL, DL
    MOV  BX, 16
    MUL  BX
    ADD  AL, LOWNUM
    OUT  PORTBC, AL       ; output to PORTBC (3rd 7-seg)

    XCHG AL, DL
    MOV  DL, 0
    MOV  AL, HIGHNUM
    DIV  CX
    XCHG AL, DL
    MOV  LOWNUM, DL
    MOV  BX, 16
    MUL  BX
    ADD  AL, LOWNUM
    OUT  PORTBA, AL       ; output to PORTBA (2nd 7-seg)

    ; Display DISPSET (similar decomposition)
    MOV  AX, DISPSET
    MOV  BX, 100
    MOV  CX, 10
    XOR  DX, DX
    DIV  BX
    MOV  HIGHNUM, AL
    XCHG AL, DL
    XOR  DX, DX
    DIV  CX
    XCHG AL, DL
    OUT  PORTAC, AL       ; leftmost digit to PORTAC

    XCHG AL, DL
    MOV  DL, 0
    MOV  BX, 16
    MUL  BX
    OUT  PORTAB, AL       ; middle digit to PORTAB

    MOV  AL, HIGHNUM
    MOV  BX, 10
    DIV  BX
    MOV  LOWNUM, AL
    XCHG AL, DL
    MOV  BX, 16
    MUL  BX
    ADD  AL, LOWNUM
    OUT  PORTBB, AL       ; rightmost digit to PORTBB

    MOV  AL, DL

    ; -------------------- Variable delay using DELAY --------------------
    PUSH CX               ; save outer counter
    MOV  CX, 0FFh         ; set new counter for delay
DelayLoop2:
    LOOP DelayLoop2
    POP  CX

    JMP MAIN
    RET
MAIN ENDP

.END MAIN
