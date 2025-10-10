---
title: "S-PRESSO: Ultra Low Bitrate Sound Effect Compression With Diffusion Autoencoders And Offline Quantization"
layout: default
paper_url: ""
affiliations:
  - "Sony AI"
  - "Télécom Paris, LTCI"
authors:
  - name: Zineb Lahrichi
    link: https://scholar.google.com/citations?hl=fr&user=YyzTTGkAAAAJ
    email: zineb.lahrichi@sony.com
    affiliations: [1, 2]
  - name: Gaëtan Hadjeres
    link: https://scholar.google.com/citations?user=wfZF3E0AAAAJ&hl=en
    affiliations: [1]
  - name: Gaël Richard
    link: https://scholar.google.com/citations?user=xn70tPIAAAAJ&hl=fr
    affiliations: [2]
  - name: Geoffroy Peeters
    link: https://scholar.google.com/citations?user=aTBWY2EAAAAJ&hl=fr
    affiliations: [2]
---

<figure class="paper-figure">
  <img src="{{ site.baseurl }}/assets/images/overview.png" alt="Overview of S-PRESSO" class="paper-figure-img">     
  <figcaption class="paper-figure-caption">
    <strong>Overview of S-PRESSO:</strong> Overview of our method.
    <strong>Step&nbsp;1:</strong> An audio clip is encoded into latent vectors \(x_0\) by a low-compression audio autoencoder.
    It is then compressed into latents \(z\), which are upsampled by \(f_\phi\) to condition the decoder \(D_\theta\), 
    a Diffusion Transformer (DiT) pretrained to reconstruct \(x_0\) from noised inputs.
    \(D_\theta\) is finetuned using LoRA adapters, jointly trained with the latent encoder \(g_\psi\) and \(f_\phi\).
    <strong>Step&nbsp;2:</strong> The features \(z\) are then quantized offline into \(z_q\).
    <strong>Step&nbsp;3:</strong> The diffusion decoder \(D_\theta\) is finetuned on \(z_q\) to compensate for quantization-induced degradation.
  </figcaption>
</figure>


Contributions: 
-  <strong>Unified continuous and discrete compression </strong>:
S-PRESSO compresses 48 kHz sound effects into both continuous and quantized latent representations, achieving up to 750× compression while maintaining perceptual fidelity.

- <strong>Diffusion-based compression</strong>: A latent diffusion decoder leverages generative priors to reconstruct high-quality audio from embeddings learned by a latent encoder.

- <strong>Extreme compression regime</strong>:
The system operates at ultra-low frame rates (down to 1 Hz) and bitrates (down to 0.096 kbps), substantially extending the limits of sound effect compression.

{% assign audio_ids = 
"BBC_LAION_footstepstonestairwomanstartecho15255, BBC_LAION_Typewriter_ClosePerspectivemediumtyping14641, EPIDEMIC_LAION_ESITUNESCrashConcreteDebris2ESCrashConcreteDebris2_68572, EPIDEMIC_LAION_ESITUNESJungleBirds7ESJungleBirds7_122481, EPIDEMIC_LAION_ESITUNESNinjaStar1ESNinjaStar1_51280, EPIDEMIC_LAION_ESITUNESSciFiVoiceClip176ESSciFiVoiceClip176_57113, FS_LAION_CupsLoud414219, FS_LAION_HollowImpact327468, FS_LAION_vatten90755, " | split: ", " %}

{% assign formats = "wav" | split: ", " %}

# Reconstruction performance 

The tables below provide audio clips for evaluating the reconstruction quality of our model in comparison to the baselines presented in the paper. The clips were chosen according to their descriptions and source datasets within the LAION 630K evaluation set, to capture the diversity of the evaluation data. We emphasize that our models were <strong> not trained </strong> on the LAION 630K training set. However, we evaluate them on a broad range of sounds (including short music excerpts) to enable a fair comparison with baselines trained on general audio.

☕  *Each audio clip is 5 seconds long. For the best experience and to notice subtle differences, we recommend listening with headphones.*

## Continuous baselines

<div markdown="0">
<table class="tableFixHead tableDoubleRows audio-table">
<colgroup>
<col/>
<col/>
<col/>
</colgroup>
<thead>
<tr class="header">
<th>  </th>
<th> Original </th>
<th> Stable Audio </th>
<th> S-PRESSO </th>
<th> Music2Latent </th>
<th> S-PRESSO </th>

</tr>
</thead>

<thead>
<tr class="header">
<th> Compression Ratio </th>
<th> / </th>
<th> 64 </th>
<th> 68 </th>
<th> 32 </th>
<th> 30 </th>

</tr>
</thead>

<thead>
<tr class="header">
<th> Framerate </th>
<th> / </th>
<th> 21.5 Hz </th>
<th> 25 Hz </th>
<th> 11 Hz </th>
<th> 11 Hz </th>

</tr>
</thead>


<tbody>
{% assign characters = "src, stableaudio, spresso25hz, music2latent, spresso11hz" | split: ", " %}
{% for audio_id in audio_ids %}
    <tr>
        <td> {{audio_id}}</td>
        {% for char in characters %}
        <td>
            <audio controls preload='metadata'>
                {% for format in formats %}
                    <source src="{{ site.baseurl }}/assets/audio/reconstruction-fidelity/{{ audio_id }}-{{ char }}.{{ format }}" 
                            type="audio/{{ format }}">
                {% endfor %}
            </audio>
        </td>
        {% endfor %}
    </tr>
{% endfor %}
</tbody> 
</table> 
</div> 

## Performance at low bitrates

<div markdown="0">
<table class="tableFixHead tableDoubleRows audio-table">
<colgroup>
<col/>
<col/>
<col/>
</colgroup>
<thead>
<tr class="header">
<th>  </th>
<th> Original </th>
<th> Descript </th>
<th> Semanticodec </th>
<th> S-PRESSO </th>

</tr>
<tr class="subheader">
<th> Bitrate  </th>
<th> / </th>
<th> 1.7 kbps </th>
<th> 1.4 kbps </th>
<th> 1.32 kbps </th>

</tr>
</thead>
<tbody>

{% assign characters = "src, descript-1.7kbps, semanticodec-1.4kbps, spresso-1.32kbps" | split: ", " %}
{% for audio_id in audio_ids %}
    <tr>
        <td> {{audio_id}}</td>
        {% for char in characters %}
        <td>
            <audio controls preload='metadata'>
                {% for format in formats %}
                    <source src="{{ site.baseurl }}/assets/audio/reconstruction-fidelity/{{ audio_id }}-{{ char }}.{{ format }}" 
                            type="audio/{{ format }}">
                {% endfor %}
            </audio>
        </td>
        {% endfor %}
    </tr>
{% endfor %}
</tbody>
</table>
</div>

## Performance at ultra-low bitrates

<div markdown="0">
<table class="tableFixHead tableDoubleRows audio-table">
<colgroup>
<col/>
<col/>
<col/>
</colgroup>
<thead>
<tr class="header">
<th>  </th>
<th> Original </th>
<th> Semanticodec </th>
<th> S-PRESSO </th>
<th> S-PRESSO </th>

</tr>
</thead>
<thead>
<tr class="header">
<th> Bitrate  </th>
<th> / </th>
<th> 0.3125 kbps </th>
<th> 0.3 kbps </th>
<th> 0.096 kbps </th>

</tr>
</thead>
<tbody>
{% assign characters = "src, semanticodec-0.3125kbps, spresso-0.3kbps, spresso-0.096kbps" | split: ", " %}
{% for audio_id in audio_ids %}
    <tr>
        <td> {{audio_id}}</td>
        {% for char in characters %}
        <td>
            <audio controls preload='metadata'>
                {% for format in formats %}
                    <source src="{{ site.baseurl }}/assets/audio/reconstruction-fidelity/{{ audio_id }}-{{ char }}.{{ format }}" 
                            alt="audio{{ audio_id }}_{{ char }}" 
                            type="audio/{{ format }}">
                {% endfor %}
            </audio>
        </td>
        {% endfor %}
    </tr>
{% endfor %}
</tbody>
</table>
</div> 

# Decoding variability 

The tables below provide audio clips for evaluating the variability of diffusion sampling for continous and discrete S-presso models across different compression rates. For each example, we provide three reconstructed samples, illustrating that increased compression amplifies variability in the generated audio, showing subtle changes in textures, high-frequency details, and background noise.

## Continuous S-PRESSO (11Hz)

{% assign audio_variability_11Hz_ids = 
"EPIDEMIC_LAION_ESITUNESFireplaceIgniteGasESFireplaceIgniteGas_66436, EPIDEMIC_LAION_ESITUNESHandSlapClaps3ESHandSlapClaps3_115779, EPIDEMIC_LAION_ESITUNESGlassBump4ESGlassBump4_116600, 
EPIDEMIC_LAION_ESITUNESPresentPickUpESPresentPickUp_36513" | split: ", " %}


<div markdown="0">
<table class="tableFixHead tableDoubleRows audio-table">
<colgroup>
<col/>
<col/>
<col/>
</colgroup>
<thead>
<tr class="header">
<th>  </th>
<th> Original </th>
<th> 1 </th>
<th> 2 </th>
<th> 3 </th>

</tr>
</thead>

<tbody>
{% assign characters = "src, gen0, gen1, gen2" | split: ", " %}
{% for audio_id in audio_variability_11Hz_ids %}
    <tr>
        <td> {{audio_id}}</td>
        {% for char in characters %}
        <td>
            <audio controls preload='metadata'>
                {% for format in formats %}
                    <source src="{{ site.baseurl }}/assets/audio/variability/s-presso-11Hz/{{ audio_id }}-{{ char }}.{{ format }}" 
                            type="audio/{{ format }}">
                {% endfor %}
            </audio>
        </td>
        {% endfor %}
    </tr>
{% endfor %}
</tbody>
</table>
</div> 

{% assign audio_variability_1Hz_ids = 
"EPIDEMIC_LAION_ESITUNESHumanBurp9ESHumanBurp9_39116, EPIDEMIC_LAION_ESITUNESPRELStingerShort22ESPRELStingerShort22_66208, EPIDEMIC_LAION_ESITUNESPRELTrailerBed60ESPRELTrailerBed60_54006, 
EPIDEMIC_LAION_ESITUNESPresentPickUpESPresentPickUp_36513" | split: ", " %}

## Continuous S-PRESSO (1Hz).

<div markdown="0">
<table class="tableFixHead tableDoubleRows audio-table">
<colgroup>
<col/>
<col/>
<col/>
</colgroup>
<thead>
<tr class="header">
<th>  </th>
<th> Original </th>
<th> 1 </th>
<th> 2 </th>
<th> 3 </th>

</tr>
</thead>

<tbody>
{% assign characters = "src, gen0, gen1, gen2" | split: ", " %}
{% for audio_id in audio_variability_1Hz_ids %}
    <tr>
        <td> {{audio_id}}</td>
        {% for char in characters %}
        <td>
            <audio controls preload='metadata'>
                {% for format in formats %}
                    <source src="{{ site.baseurl }}/assets/audio/variability/s-presso-1Hz/{{ audio_id }}-{{ char }}.{{ format }}" 
                            type="audio/{{ format }}">
                {% endfor %}
            </audio>
        </td>
        {% endfor %}
    </tr>
{% endfor %}
</tbody>
</table>
</div> 

{% assign audio_variability_03kbps_ids = 
"FS_LAION_WoodpeckerinwoodedsuburbanvillagenearMoscow432533, FS_LAION_FenderChromaPolarisSquareFilterE352E29CHT289445, EPIDEMIC_LAION_ESITUNESLaserShotSynth105ESLaserShotSynth105_137627, EPIDEMIC_LAION_ESITUNESCabinetDoorOpen16ESCabinetDoorOpen16_91831" | split: ", " %}

## Discrete S-PRESSO (1Hz, 0.3 kbps)


<div markdown="0">
<table class="tableFixHead tableDoubleRows audio-table">
<colgroup>
<col/>
<col/>
<col/>
</colgroup>
<thead>
<tr class="header">
<th>  </th>
<th> Original </th>
<th> 1 </th>
<th> 2 </th>
<th> 3 </th>

</tr>
</thead>

<tbody>
{% assign characters = "src, g0, g1, g2" | split: ", " %}
{% for audio_id in audio_variability_03kbps_ids %}
    <tr>
        <td> {{audio_id}}</td>
        {% for char in characters %}
        <td>
            <audio controls preload='metadata'>
                {% for format in formats %}
                    <source src="{{ site.baseurl }}/assets/audio/variability/s-presso-1Hz-03kbps/{{ audio_id }}-{{ char }}.{{ format }}" 
                            type="audio/{{ format }}">
                {% endfor %}
            </audio>
        </td>
        {% endfor %}
    </tr>
{% endfor %}
</tbody>
</table>
</div> 

## Discrete S-PRESSO (1Hz, 0.096 kbps)

<div markdown="0">
<table class="tableFixHead tableDoubleRows audio-table">
<colgroup>
<col/>
<col/>
<col/>
</colgroup>
<thead>
<tr class="header">
<th>  </th>
<th> Original </th>
<th> 1 </th>
<th> 2 </th>
<th> 3 </th>

</tr>
</thead>

<tbody>
{% assign characters = "src, g0, g1, g2" | split: ", " %}
{% for audio_id in audio_variability_03kbps_ids %}
    <tr>
        <td> {{audio_id}}</td>
        {% for char in characters %}
        <td>
            <audio controls preload='metadata'>
                {% for format in formats %}
                    <source src="{{ site.baseurl }}/assets/audio/variability/s-presso-1Hz-0.096kbps/{{ audio_id }}-{{ char }}.{{ format }}" 
                            type="audio/{{ format }}">
                {% endfor %}
            </audio>
        </td>
        {% endfor %}
    </tr>
{% endfor %}
</tbody>
</table>
</div>
