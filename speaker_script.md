# Speaker Script

This is an example of a script which can be used to deliver custom responses.

* It uses the speaker closest to the user
* It switches from cloud-based voice to local voice if the internet is down
* It corrects a bug in PicoTTS when that is being used
```
alias: TTS response
sequence:
  - if:
      - condition: state
        entity_id: input_boolean.use_cloud_service
        state: "off"
    then:
      - target:
          entity_id:
            - "{{ states('sensor.speaker') }}"
        data:
          announce: true
          media_content_id: >
            media-source://tts/picotts?message="{{ tts_sentence
            }}................................"
          media_content_type: music
          extra:
            volume: 50
        action: media_player.play_media
    else:
      - action: tts.speak
        metadata: {}
        data:
          cache: true
          media_player_entity_id: "{{ states('sensor.speaker') }}"
          message: "{{ tts_sentence }}"
          options:
            voice: XYZ
        target:
          entity_id: tts.elevenlabs
        enabled: true
fields:
  tts_sentence:
    selector:
      text: {}
    name: TTS_sentence
```
## Notes

```input_boolean.use_cloud_service``` is a flag - an automation turns it on when the internet is available, off when it isn't.

```sensor.speaker``` is a template sensor holfing the entity ID of the speaker nearest the user. 
There are several ways to figure out where the user is - motion sensors are quite effective, or you can use a tracking integration like [Bermuda](https://github.com/agittins/bermuda).













