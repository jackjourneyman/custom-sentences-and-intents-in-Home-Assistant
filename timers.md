# Timers

Built-in sentences for timers only work on ESPHome and Wyoming voice satellites, and even then the timer runs locally on the device - they are not exposed as entites in Home Assistant itself - which means you can't show progress on a dashboard, for example.

You can control timers previously created in HA with the [timer helper](https://www.home-assistant.io/integrations/timer/), however.

# Start timer
```
CustomTimerSet:
  action:
    - action: script.voice_timer_2
      data:
        duration: "{{hours | int(default=0)}}:{{ minutes | int(default=0) }}:{{ seconds | int(default=0) }}"
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer started."
```
# Stop timer
```
CustomTimerCancel:
  action:
    - action: script.voice_timer_cancel
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer cancelled."
```
# Pause timer
```
CustomTimerPause:
  action:
    - action: script.voice_timer_pause
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer paused."
```
# Resume timer
```
CustomTimerResume:
  action:
    - action: script.voice_timer_resume
    - action: script.willow_tts_response
      data:
        tts_sentence: "{{ states('sensor.finished_phrase') }}. Timer started again."
```
