;CPE-311-Final-Project
=====================

;CPE Lab Elevator Project

Assume cs:code, ds:data, ss:stack

data segment 

; All variables defined within the data segment
message db "Please use the keyboard to input your destination floors (options 1-5)$"
reached1 db "We have reached Floor 1! Have a nice day!$"
reached2 db "We have reached Floor 2! Have a nice day!$"
reached3 db "We have reached Floor 3! Have a nice day!$"
reached4 db "We have reached Floor 4! Have a nice day!$"
reached5 db "We have reached Floor 5! Have a nice day!$"
STORAGE db 3 DUP(" ")
var db "w"


data ends
  

stack segment  
;Stack size and the top of the stack defined in the stack segment
dw 100 dup(?)
stacktop:
stack ends

code segment
begin:    
mov ax, data
mov ds,ax
mov ax, stack
mov ss,ax
mov sp, offset stacktop
;THIS IS WHERE YOUR CODE BEGINS

;begin code



receivefloorcall:

MOV DX, 143H   ; Specify the Control Register
MOV AL, 02H     ; Value of 2 used for DIRECTION mode
OUT DX, AL       ; Send the value 2 to the Control Register

MOV DX, 141H   ; Specify Port B
MOV AL, 11111111B    ; 1111 1111 sets upper bits as inputs(0’s), lower bits as outputs(1’s)
OUT DX, AL       ; Sends value 1111 1111 to Port B to establish I/O directions

MOV DX, 143H   ; Specifies the Control Register
MOV AL, 03H     ; Value of 3 used for OPERATION mode
OUT DX, AL       ; Sends the value 3 to the Control Register, now in OPERATION mode

MOV DX, 143H   ; Specify the Control Register
MOV AL, 02H     ; Value of 2 used for DIRECTION mode
OUT DX, AL       ; Send the value 2 to the Control Register

MOV DX, 142H   ; Specify Port C
MOV AL, 0FFH    ; 1111 1111 sets upper bits as inputs(0’s), lower bits as outputs(1’s)
OUT DX, AL       ; Sends value 1111 1111 to Port B to establish I/O directions

MOV DX, 143H   ; Specifies the Control Register
MOV AL, 03H     ; Value of 3 used for OPERATION mode
OUT DX, AL       ; Sends the value 3 to the Control Register, now in OPERATION mode


MOV DX, 143H    ; Place I/O chip into direction mode
MOV AL, 2    
OUT DX, AL 

MOV DX, 140H    ; Send 0000 0000 to Port A (all inputs)
MOV AL, 00H
OUT DX, AL

MOV DX, 143H    ; Place I/O chip into operation mode
MOV AL, 3
OUT DX, AL

MOV DX, 140H    ; Move port # into dx, in this case, 140h for Port A
IN AL, DX        ; Receives the input values at Port A and stores them in AL (8 bits)

MOV DX, 140H    ; Port A
IN al, dx



CMP al, 11111111B
JE receivefloorcall

Call FloorLight1
Call Delay1Sec
Call Delay1Sec

CMP Al, 11111110B ;check if call from floor 1
JNE twoD
Call UP1
Call Delay1Sec
Call Delay1Sec
JE Elevatormoves

twoD:
CMP AL, 11111101B  ;check if call from floor 2 down
JNE twoU
Call DOWN2
Call Delay1Sec
Call Delay1Sec
JE Elevatormoves

twoU:
CMP AL, 11111011B ;check if call from floor 2 up
JNE threeD
Call UP2
Call Delay1Sec
Call Delay1Sec
JE Elevatormoves

threeD:
CMP AL, 11110111B ;check if call from floor 3 down
JNE threeU
Call DOWN3
Call Delay1Sec
Call Delay1Sec
JE Elevatormoves

threeU:
CMP AL, 11101111B ;check if call from floor 3 up 
JNE fourD
Call UP3
Call Delay1Sec
Call Delay1Sec
JE Elevatormoves

fourD:
CMP AL, 11011111B ;check if call from floor 4 down
JNE fourU
Call DOWN4
Call Delay1Sec
Call Delay1Sec
JE Elevatormoves

fourU:
CMP AL, 10111111B 
JNE fiveD
Call UP4
Call Delay1Sec
Call Delay1Sec
JE Elevatormoves

fiveD:
Call DOWN5
Call Delay1Sec
Call Delay1Sec

Elevatormoves:
CMP Al, 11111110B ;check if call from floor 1
JE inputfloors

Call FloorLight2GoingUp
Call Delay1Sec
Call Delay1Sec
CMP AL, 11111101B  ;check if call from floor 2 down
JE inputfloors

Call FloorLight2GoingUp
Call Delay1Sec
Call Delay1Sec
CMP AL, 11111011B ;check if call from floor 2 up


JE inputfloors


Call FloorLight3GoingUp
Call Delay1Sec
Call Delay1Sec
CMP AL, 11110111B ;check if call from floor 3 down

JE inputfloors

Call FloorLight3GoingUp
Call Delay1Sec
Call Delay1Sec
CMP AL, 11101111B ;check if call from floor 3 up 

JE inputfloors

Call FloorLight4GoingUp
Call Delay1Sec
Call Delay1Sec
CMP AL, 11011111B ;check if call from floor 4 down

JE inputfloors


Call FloorLight4GoingUp
Call Delay1Sec
Call Delay1Sec
CMP AL, 10111111B ;check if call from floor 4 up 

JE inputfloors


Call FloorLight5
Call Delay1Sec
Call Delay1Sec

inputfloors:
;INCREMENT THROUGH STORAGE VARIABLE?
;COMPARE STATEMENTS DECIDE WHAT FLOOR THE USER ASKED FOR

call newInput
call newInput
call newInput

mov dl, i1
call startComparing
mov dl, i2
call startComparing
mov dl, i3
call startComparing
JMP end_Prog

startcomparing:

cmp dl, 00H
JE nextInput1
cmp dl, 31H
JE compare1
cmp dl, 32H
JE compare2
cmp dl, 33H
JE compare3
cmp dl, 34H
JE compare4
cmp dl, 35H
JE compare5

;COMPARE STATEMENTS TO SEE IF THE ELEVATOR MUST TRAVEL UP OR DOWN BASED ON WHERE IT WAS PREVIOUSLY CALLED TO (BECAUSE WE'RE CURRENTLY THERE)
compare1:
CMP al, 11111110B
JG goDownToFirst
CMP al, 11111110B
JE arriveat1down
CMP al, 11111110B
JNE nextInput1

compare2:
CMP al, 11111101B
JL goUpToSecond
CMP al, 11111101B
JE arriveat2up
CMP al, 11111011B
JG goDownToSecond
CMP al, 11111011B
JE arriveat2down
CMP al, 11111011B
JNE nextInput1


compare3:
CMP al, 11110111B
JL goUpToThird
CMP al, 11110111B
JE arriveat3Up
CMP al, 11101111B
JG goDownToThird
CMP al, 11101111B
JE arriveat3Down
CMP al, 11101111B
JNE nextInput1


compare4:

CMP al, 11011111B

JL goUpToFourth
CMP al, 11011111B
JE arriveat4Up

CMP al, 10111111B

JG goDownToFourth
CMP al, 10111111B
JE arriveat4Down
CMP al, 10111111B
JNE nextInput1

compare5:

CMP al, 01111111B

JL goUpToFive
CMP al, 01111111B
JE arriveat5
CMP al, 01111111B
JNE nextInput1

;TRAVEL DOWN TO FIRST Floor
goDownToFirst:

not al
checkarrivaldown1:

shr al, 1
;SKIP THE TOP FLOOR SINCE BITS ALREADY SHIFTED RIGHT
cmp al, 01000000B
JE turnon4down1
cmp al, 00100000B
JE turnon4down1
cmp al, 00010000B
JE turnon3down1
cmp al, 00001000B
JE turnon3down1
cmp al, 00000100B
JE turnon2down1
cmp al , 00000010B
JE turnon2down1
cmp al , 00000001B
JE arriveat1down

;TURN ON FLOOR 4 DOWN
turnon4down1:
Call FloorLight4GoingDown
Call Delay1Sec
Call Delay1Sec
JMP checkarrivaldown1

;TURN ON FLOOR 3 DOWN
turnon3down1:
Call FloorLight3GoingDown
Call Delay1Sec
Call Delay1Sec
JMP checkarrivaldown1

;TURN ON FLOOR 2 DOWN
turnon2down1:
Call FloorLight2GoingDown
Call Delay1Sec
Call Delay1Sec
JMP checkarrivaldown1

;SUCCESS
arriveat1down:
not al
mov si, offset reached1
Call Print
Call FloorLight1
Call Delay1Sec
Call Delay1Sec
JMP nextInput1

;TRAVEL UP TO FLOOR 2
goUpToSecond:
not al
checkarrivalup2:
shl al, 1
;ONLY ONE OPTION

;SUCCESS
arriveat2up:
not al
mov si, offset reached2
Call Print
Call FloorLight2GoingUp
Call Delay1Sec
Call Delay1Sec
JMP nextInput1

;TRAVEL DOWN TO FLOOR TWO
goDownToSecond:
not al
checkarrivaldown2:
shr al , 1
;SKIP TOP Floor
cmp al, 01000000B
JE turnon4down2
cmp al, 00100000B
JE turnon4down2
cmp al, 00010000B
JE turnon3down2
cmp al, 00001000B
JE turnon3down2
cmp al, 00000100B
JE arriveat2down

;TURN ON FLOOR 4 GOING DOWN
turnon4down2:
Call FloorLight4GoingDown
Call Delay1Sec
Call Delay1Sec
JMP checkarrivaldown2

;TURN ON FLOOR 3 GOING DOWN
turnon3down2:
Call FloorLight3GoingDown
Call Delay1Sec
Call Delay1Sec
JMP checkarrivaldown2

;SUCCESS
arriveat2down:
not al
mov si, offset reached2
Call Print
Call FloorLight2GoingDown
Call Delay1Sec
Call Delay1Sec
JMP nextInput1

;TRAVEL UP TO FLOOR 3
goUpToThird:
not al
checkarrivalup3:
shl al, 1
;NO NEED TO CHECK FIRST FLOOR SINCE ALREADY SHIFTED BITS LEFT ONCE
cmp al, 00000010B
JE turnon2up3
cmp al, 00000100B
JE turnon2up3
cmp al, 00001000B
JE arriveat3Up

;TURN ON FLOOR 2 UP
turnon2up3:
Call FloorLight2GoingUp
Call Delay1Sec
Call Delay1Sec
JMP checkarrivalup3

;SUCCESS
arriveat3Up:
not al
mov si, offset reached3
Call Print
Call FloorLight3GoingUp
Call Delay1Sec
Call Delay1Sec
JMP nextInput1

;GO DOWN TO FLOOR 3
goDownToThird:
not al
checkarrivaldown3:
shr al, 1
;NO NEED TO COMPARE TO TOP Floor
cmp al, 01000000B
JE turnon4down3
cmp al, 00100000B
JE turnon4down3
cmp al, 00010000B
JE arriveat3Down

;TURN ON 4 DOWN
turnon4down3:
Call FloorLight4GoingDown
Call Delay1Sec
Call Delay1Sec
JMP checkarrivaldown3

;SUCCESS
arriveat3Down:
not al
mov si, offset reached3
Call Print
Call FloorLight3GoingDown
Call Delay1Sec
Call Delay1Sec
JMP nextInput1

;TRAVEL UP TO FLOOR 4
goUpToFourth:
not al
checkarrivalup4:
shl al, 1
;NO NEED TO CHECK FIRST Floor
cmp al, 00000010B
JE turnon2up4
cmp al, 00000100B
JE turnon2up4
cmp al, 00001000B
JE turnon3up4
cmp al, 00010000B
JE turnon3up4
cmp al, 00100000B
JE arriveat4Up

;FLOOR 2 UP ON
turnon2up4:
Call FloorLight2GoingUp
Call Delay1Sec
Call Delay1Sec
JMP checkarrivalup4

;TURN ON FLOOR 3 UP
turnon3up4:
Call FloorLight3GoingUp
Call Delay1Sec
Call Delay1Sec
Jmp checkarrivalup4

;SUCCESS
arriveat4Up:
not al
mov si, offset reached4
Call Print
Call FloorLight4GoingUp
Call Delay1Sec
Call Delay1Sec
JMP nextInput1

;TRAVEL DOWN TO FLOOR 4
goDownToFourth:
not al
checkarrivaldown4:
;ONLY ONE OPTION
shr al, 1

;SUCCESS

arriveat4Down:
not al
mov si, offset reached4
Call Print
Call FloorLight4GoingDown
Call Delay1Sec
Call Delay1Sec
JMP nextInput1

;TRAVEL UP TO FLOOR 5
goUpToFive:
not al
checkarrivalup5:
shl al, 1
;NO NEED TO COMPARE TO FIRST Floor
cmp al, 00000010B
JE turnon2up5
cmp al, 00000100B
JE turnon2up5
cmp al, 00001000B
JE turnon3up5
cmp al, 00010000B
JE turnon3up5
cmp al, 00100000B
JE turnon4up5
cmp al, 01000000B
JE turnon4up5
cmp al, 10000000B
JE arriveat5

;TURN ON 2 UP
turnon2up5:
Call FloorLight2GoingUp
Call Delay1Sec
Call Delay1Sec
JMP checkarrivalup5

;TURN ON 3 UP
turnon3up5:
Call FloorLight3GoingUp
Call Delay1Sec
Call Delay1Sec
JMP checkarrivalup5

;TURN ON 4 UP
turnon4up5:
Call FloorLight4GoingUp
Call Delay1Sec
Call Delay1Sec
JMP checkarrivalup5

;SUCCESS
arriveat5:
not al
mov si, offset reached5
Call Print
Call FloorLight5
Call Delay1Sec
Call Delay1Sec
;CODE GOES INTO LOOP WHICH WILL ASK THE ELEVATOR TO MOVE TO NEXT INPUTTED Floor

nextInput1:
Ret

newInput:
Push AX
Push bx

Mov si, offset message
;PRINT MESSAGE ASKING FOR USER INPUT
call Print



;STORE IN STORAGE VARIABLE
mov di, offset STORAGE
;RECEIEVE INPUT STORED IN AL
mov bl, 01H
mov AH,1
int 21H 

mov dl, al
Pop AX
pop bx
ret




Up1:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

Mov DX, 142H
mov al, 11111110B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

DOWN2:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

Mov DX, 142H
mov al, 11111101B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

UP2:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

Mov DX, 142H
mov al, 11111011B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called


DOWN3:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

Mov DX, 142H
mov al, 11110111B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

UP3:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

Mov DX, 142H
mov al, 11101111B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called


DOWN4:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

Mov DX, 142H
mov al, 11011111B
out dx,al


POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

UP4:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

Mov DX, 142H
mov al, 10111111B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called


DOWN5:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

Mov DX, 142H
mov al, 01111111B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called


FloorLight1:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

Mov dx, 141H
Mov al, 10111110B
And al, 01111111B
out dx,al


POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

FloorLight2GoingUp:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

mov dx, 141H
Mov al, 01000010B
not al
and al, 01111111B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

FloorLight2GoingDown:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

mov dx, 141H
Mov al, 00100010B
not al
and al, 01111111B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

FloorLight3GoingUp:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

mov dx, 141H
Mov al, 01000100B
not al
and al, 01111111B
out dx,al

POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

FloorLight3GoingDown:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

mov dx, 141H
Mov al, 00100100B
not al
and al, 01111111B
out dx,al


POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

FloorLight4GoingUp:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

mov dx, 141H
Mov al, 01001000B
not al
and al, 01111111B
out dx,al


POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

FloorLight4GoingDown:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

mov dx, 141H
Mov al, 00101000B
not al
and al, 01111111B
out dx,al


POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called

FloorLight5:
PUSH AX    ; Places AX onto the top of the stack
PUSH DX    ; Places DX onto the top of the stack

mov dx, 141H
Mov al, 00110000B
not al
and al, 01111111B
out dx,al


POP DX    ; Pops the value on the top of the stack into DX
POP AX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called





Print:
PUSH AX	; Places AX onto the top of the stack
PUSH DX	; Places DX onto the top of the stack
mov ah, 2
abc:
Mov dl,[si]
Cmp dl,"$"
je continue
Inc si
Int 21h
Jmp abc

continue:
;move to new line for next time
MOV AH,2
MOV DL,0DH	;ASCII equivalent of carriage return
INT 21H  ;will perform carriage return. i.e. brings cursor to first position on same line just printed
MOV DL,0AH  ;ASCII equivalent of line feed/line break/new line
INT 21H	;Moves cursor to beginning of the next line.
;the effect created by these two ASCII prints allows any future string to be ;printed on a new line for better readability


POP DX	; Pops the value on the top of the stack into DX
POP AX  	; Pops the value on the top of the stack into AX
RET		; Return program execution to the point where the subroutine was called


Delay1Sec:
PUSH BX    ; Places AX onto the top of the stack
PUSH CX    ; Places DX onto the top of the stack

mov bx, 10
outer1:
mov cx, 0FA106H
inner1:
nop
loop inner1
dec bx
jnz outer1

POP CX    ; Pops the value on the top of the stack into DX
POP BX      ; Pops the value on the top of the stack into AX
RET        ; Return program execution to the point where the subroutine was called


End_prog:
Mov ah, 4ch
Int 21h
;end code


;THIS IS WHERE YOUR CODE ENDS
code ends
end begin
