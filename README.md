# ProvokArt

Automatic speech recognition in a VR art gallery. This application is based on [#MarkartNow](https://www.hamburger-kunsthalle.de/makartnow), a civic participation project of the **Hamburger Kunsthalle**, and has been developed for an Oculus Head-Mounted Display, using Unity Editor v. 2021.3.24f1. The source code can be downloaded from the Release panel.

<p align="center">
<img src="https://github.com/ManueleVeggi/provokart_vr/blob/d52dbbf02e54dc24501e9912135cc8981c67b257/OpeningScreenshot.png" style="height:25rem">
</p>

Final Project of "Laboratory of Virtual and Augmented Reality" (Prof. Gustavo Marfia) at University of Bologna (a.y. 2022/2023). 

# Documentantion

## Table of content 
- [**Introduction**](#introduction)
- [**Design and Use Case**](#usecase)
- [**Software Architecture**](#softarchi)
- [**Criticalities**](#criticalities)

## 1. Introduction <a name="introduction"></a>

"ProvokArt" is a VR experience for Oculus HMD, based on the initiative #MakartNow of the Hamburger Kunsthalle. This project consists of a set of provocative questions which have been added to traditional explanatory labels: their function is to suggest visitors new possible interpretations, highlighting what a collection of 19th century painting can still teach us about our society.

The core function of "ProvokArt" is an automatic speech recognition system, which satisfies a more and more demanded requirement in interaction design in the cultural heritage sector, i.e. the possibility of evaluating <i>in itinere</i> the prototypes, gathering data directly from the visitors. Through the verbalization process it is indeed possible to analyze the fulfillment of specific cognitive goals: transcribing the speech of the visitors and collecting it in a corpus is hence a valuable resource.

## 2. Design and Use Case <a name="usecase"></a>

Although the first proposal foresaw a virtual explorable gallery, the design of the application was considerably changed after the familiarization with the visualization systems of the Laboratory of Experimental Museology at the EPFL of Lausanne and the recognition of existing data for the 3D reconstruction of the exhibition hall. 

The website of the museum indeed only provides a panoramic view in low resolution of the main hall (Makartssaal), which was eventually used as skybox of the final UX. The absence of a sufficient amount of data and the awareness that any manual reconstruction of the room would be inaccurate led to an alternative choice. Following the suggestions provided by Prof. Kenderdine during the course "Cultural Data Sculpting", the pieces of information have been displayed mimicking the Object Navigator, a vertical screen installed at the center of a turning circular platform which allows to <i>navigate</i> an object at 360°. 

<p align="center">
<img src="AdditionalMaterial/ObjectNavigator.jpg">
</p>
The seven sections in which the project #MakartNow has been articulated and an initial welcoming section are displayed in an octagonal shape, which can be rotated by the visitors thanks to the arrows in the interactive UI. Each section contains just a title and a subtitle: the paintings and the questions can be accessed through a specific button in another section. 

Clicking on this element it is indeed possible to open a new canvas, outside the octagonal navigator, which contains for each section the corresponding paintings and questions (in the graph, "section page"). A last button allows to open a new canvas where these questions are re-proposed and visitors can transcribe their answers and save it as txt file.

<p align="center">
<img src="AdditionalMaterial/DesignElements.png">
</p>

As txt format can be easily processed by NLP libraries (in particular in Python), the UX does not aim exclusively at extending a non-digital born museum initiative into a virtual and immersive environment, but also to provide a useful evaluation tool for designers:

<p align="center">
<img src="AdditionalMaterial/Use case.png">
</p>

## 3. Software Architecture <a name="softarchi"></a>

Comparing to the original [proposal](https://github.com/ManueleVeggi/provokart_vr/blob/30c5752f6fc384ea2a7d12e9d7b768b83f432727/AdditionalMaterial/Veggi%20Proposta%20Progetto%20LabMarfia.pdf), the main change pertains the target device. As said, the UX is developed for Oculus VR. Besides an apparent incompatibility between HTC Vive and the operating system of my machine, this choice has been supported by Prof. Pescarin, my supervisor, as the laboratory she is affiliated to (CNR ISPC) works primarily with this typology of devices. Secondly, the preparation of this project required the attendance of three different Unity Pathways: Essential, Junior Developer, and VR Development, which was focused primarily on Oculus Meta devices. The UX has been tested exclusively with a XR Device Simulator.

The project consists mainly of interactive UI which are nested and grouped into the following objects:
- UI Manager: empty object articulated in
    - Octagonal Navigator: consists of a prism with octagonal basis (from Sketchfab) which is invisible as the material has been removed. It contains the "Welcome" and the seven "Section Presentation" pages;
    - Main Content Canvas Manager: an empty object collecting credits, "Section" pages and ASR page;

Toggling between these GameObjects is made possible through part of the methods defined in the ```UIManager.cs```. The correct set up of the experience (at the beginning, only the octagonal navigator should be active) is indeed handled by ```Start``` (which is also responsible for the creation of the folder where the final transcriptions will be saved). Through the buttons of the UI it is possible to perform:
- ```OpenMainCanvas(GameObject canvasToDisplay)```: from every page of the OctagonalNavigator to the corresponding one of the MainContentCanvasManager (either credits or "Section" pages);
- ```BackToOctagon()```: from any page of the MainContentCanvasManager to the OctagonalNavigator;
- ```GoToASR(int index)```: from the "Section" pages of the MainContentCanvasManager to the ASR page. It is also responsible for reporting the questions visitors are asked to answer to;

The script also contains ```RotateOctagon(bool dir)``` to rotate the initial octagonal navigator. It also defines a method to save the transcribed answer of the visitor (see later).

To what pertains the Automatic Speech Recognition, the choice of the Oculus as target device allows to reuse <i>ad hoc</i> SDK, without adopting other strategies (such as the ones described in the proposal presentation). Meta already developed [Voice SDK](https://developer.oculus.com/documentation/unity/voice-sdk-overview/): following the guidelines provided in the [documentation](https://developer.oculus.com/documentation/unity/voice-sdk-transcription/), an "App Dictation Experience" was added after the creation of a dedicated application on Wit.AI. The transcribed answer is saved into .txt through the method ```SaveAnswer(TextMeshProUGUI transcribedAnswer)```.

## 4. Criticalities <a name="criticalities"></a>

A possible limitation of the UX is given by the ASR service, which may incur in some minor errors in the transcription and is not able to deal with complex and long texts. Moreover, it does not manage to tackle code switching phenomena. A robust analysis of the collected corpus hence would require a manual revision of the transcribed utterances.

To what pertains the usability, the project has been developed considering testing session through Unity XR Device Simulator and not a real Oculus HMD. Some choices of the device could be improved consider this target device for final testing: among this, enabling the visitor to rotate the navigator using directly the controller (similarly to a "drag and drop"), without using the arrows. In addition, transition between different canvas could be smoother lerping the rotation and fading the toggle between two panels.
