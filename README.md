# Wio_Node with Grove PIR Motion Driver

## Introduction
The Grove PIR Motion Module has a driver that will fire and event when the motion is detected.

### Event Definition
GrovePIRMotion	"GET /v1/node/GrovePIRMotionD0/approach -> uint8_t approach"
"Event GrovePIRMotionD0 ir_moved"
The PIR Motion interrupt handler in the Driver will actually post the event. Here is a snippet of this code in the driver:
```cpp
static void pir_motion_interrupt_handler(void *para)
{
    GrovePIRMotion *g = (GrovePIRMotion *)para;
    if (millis() - g->time < 10)
    {
        return;
    }
    g->time = millis();
    POST_EVENT_IN_INSTANCE(g, ir_moved, g->io);
}
```

## Event Code
You can upload code to the and use the ULB ( User Logic Block) feature of Wio Link. The ULB is a piece of code that can be uploaded to the OTA server of Wio and be compiled at the OTA server, then be OTA to the Wio board.

Essentially you can write code that runs every 100ms, captures any events from the Grove Module, in this case PIR, and runs some code.
> ! this is not a complete code example, so don't copy/paste it.

```cpp

#include "wio.h"
#include "suli2.h"
#include "Main.h"

uint32_t time;
event_t event;

void setup()
{
    time = millis();
}

void loop()
{
    if(millis() - time > 100) {
        if (wio.eventAvailable())
        {
            wio.getEvent(&event);

            if (strcmp(event.event_name, "ir_moved") == 0)
            {
                uint8_t event_data = event.event_data.s8;
                //Do something with event_data...
            }           
        }
    }
    
}
```

### Upload ULB
Here is a sample of uploading the code to the OTA server using a pythonng

> ! this is not a complete script example, so don't copy/paste it.

```bash
$ ./ulb_helper.py set https://cn.wio.seeed.io 9bfc9a8c9c2c975bb4b770ca0fd088b1
{'Main.cpp': '#include "wio.h"\n#include "suli2.h"\n#include "Main.h"\n\nuint32_t time;\nint light;\n\nvoid setup()\n{\n    time = millis();...', 'Main.h': '#ifndef __MAIN_H__\r\n#define __MAIN_H__\r\n#include "suli2.h"\r\n'}


=> Posting contents...
=> Success!

```
