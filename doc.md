# Documentantion

## 1. Introduction

"ProvokArt" is a VR experience for Oculus HMD, based on the initiative #MakartNow of the Hamburger Kunsthalle. This project consists of a set of provocative questions which have been added to traditional explanatory labels: their function is to suggest visitors new possible interpretations, highlighting what a collection of 19th century painting can still teach us about our society.

The core function of "ProvokArt" is an automatic speech recognition system, which satisfies a more and more demanded requirement in interaction design in the cultural heritage sector, i.e. the possibility of evaluating <i>in itinere</i> the prototypes, gathering data directly from the visitors. Through the verbalization process it is indeed possible to analyze the fulfillment of specific verbalization goals: transcribing the speech of the visitors and collecting it in a corpus is hence a valuable resource.

## 2. Design and Use case

Although the first proposal foresaw a virtual explorable gallery, the design of the application was considerably changed after the familiarization with the visualization systems of the Laboratory of Experimental Museology at the EPFL of Lausanne and the recognition of existing data for the 3D reconstruction of the exhibition hall. 

The website of the museum indeed only provides a panoramic view in low resolution of the main hall (Makartssaal), which was eventually used as skybox of the final UX. The absence of a sufficient amount of data and the awareness that any manual reconstruction of the room would be inaccurate led to an alternative choice. Following the suggestions provided by Prof. Kenderdine during the course "Cultural Data Sculpting", the informations have been displayed following the metaphore of the Object Navigator, a vertical screen based at the center of a turning circular platform which allows to <i>navigate</i> an object at 360°. 

<img src="AdditionalMaterial/ObjectNavigator.jpg">

The seven sections in which the project #MakartNow has been articulated and an initial welcoming section are displayed in turning octagonal shape. Users can incrementally rotate thanks to arrows in the interactive UI. Each section contains just a title and a subtitle: the paintings and the sections can be accessed through a specific button. 

Clicking on this element it is indeed possible to open a new canvas, outside the octagonal navigator, which contains for each section the corresponding paintings and questions (in the graph, "section page"). A last button allows to open a new canvas where these questions are re-proposed and visitors can transcribe their answers and save it as txt file.

<img src="AdditionalMaterial/DesignElements.png">

As txt format can be easily processed by NLP libraries (in particular in Python), the UX does not aim exclusively at extending a non-digital born museum initiative into a virtual and immersive environment, but also to provide a useful evaluation tool for designer:

<img src="AdditionalMaterial/Use case.png">

## 3. Software Architecture

Comparing to the original proposal, the main change pertains the target device. As said, the UX is developed for Oculus VR. Besides an apparent incompatibility between HTC Vive and my operating system, this choice has been supported by Prof. Pescarin, my supervisor, as the laboratory she is affiliated to (CNR ISPC) works primarily with this typology of devices. Secondly, the preparation of this project required the attendance of three different Unity Pathways: Essential, Junior Developer, and VR Development, which was based primarily for Oculus Meta devices.

The project consists mainly of interactive UI which are nested and grouped into the following objects:
- UI Manager: empty object articulated 
    - Octagonal Navigator: consists of a prism with octagonal basis (from Sketchfab) which is invisible as the material is removed. It contains the "Welcome" and the seven "Section Presentation" pages;
    - Main Content Canvas Manager: an empty object collecting credits, "Section" pages and ASR page;

****
**DRAFT**

App Dictation Experience

```
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.UI;
using System.IO;
using TMPro;
using UnityEditor;
using System;

public class UIManager : MonoBehaviour
{
    public GameObject octagonalManager;
    public GameObject mainCanvasManager;

    public GameObject canvasASR;
    public TextMeshProUGUI questionsText;

    private void Start()
    {
        Directory.CreateDirectory(Application.streamingAssetsPath + "/Export/");
        octagonalManager.SetActive(true);
        mainCanvasManager.SetActive(false);
    }

    public void OpenMainCanvas(GameObject canvasToDisplay)
    {
        octagonalManager.SetActive(false);

        mainCanvasManager.SetActive(true);
        foreach (Transform child in mainCanvasManager.transform)
        {
            child.gameObject.SetActive(false);
        }

        canvasToDisplay.SetActive(true);

    }

    public void BackToOctagon()
    {
        mainCanvasManager.SetActive(false);
        octagonalManager.SetActive(true);
    }


    public void RotateOctagon(bool dir)
    {
        if(dir)
        {
            Quaternion targetRotation = Quaternion.Euler(transform.rotation.eulerAngles.x + 90f, octagonalManager.transform.rotation.eulerAngles.y - 45f,transform.rotation.eulerAngles.z);
            octagonalManager.transform.rotation = targetRotation;
        }
        else
        {
            Quaternion targetRotation = Quaternion.Euler(transform.rotation.eulerAngles.x + 90f, octagonalManager.transform.rotation.eulerAngles.y + 45f, transform.rotation.eulerAngles.z);
            octagonalManager.transform.rotation = targetRotation;
        }
        //octagonalManager.transform.rotation *= Quaternion.Euler(0f, 45f, 0f);
    }

    public void SaveAnswer(TextMeshProUGUI transcribedAnswer)
    {
        DateTime theTime = DateTime.Now;

        string txtDocumentName = Application.streamingAssetsPath + "/Export/" + "Answer_" + theTime.ToString("yyyy_MM_dd\\THH_mm_ss\\Z") + ".txt";

        File.WriteAllText(txtDocumentName, "Questions: \n\n");
        File.AppendAllText(txtDocumentName, questionsText.text + "\n\n");
        File.AppendAllText(txtDocumentName, transcribedAnswer.text + "\n");

        AssetDatabase.Refresh();
    }

    public void GoToASR(int index)
    {
        List<string> currentQuestions = new List<string>
        {
            "Do you find the painting provocative? Do you think it's sexist?\nFacts - are they as important as immagination?\nHow do you see power depicted here?",
            "Can History painting build identity?\nDo these historical people / situations trigger your compassion?\nWould you have imagined Napoleon like this?",
            "Is that love of detail or a painted film set?\nWas everything really better in the past?\nIs that backward-looking or just nostalgic?",
            "Who is the fairest of them all, and who can judge?\nFemale power - Male power?\nIs that admiration, voyeurism or sexism?",
            "Do you think that's kitschy or moving?\nWhat if the first impression is deceptive?\nDoes it still touch us today?",
            "Is it authentic or staged?\nDistant lands - a familiar cliché?\nDo you believe what you see?",
            "Is that Art or kitsch?\nIs that instagrammable?\nWould you share these views on social media?"
        };

        questionsText.text = currentQuestions[index];

        foreach (Transform child in mainCanvasManager.transform)
        {
            child.gameObject.SetActive(false);
        }

        canvasASR.SetActive(true);
    }
}
```

## 4. Criticalities and further work 
Commento rispetto alle possibili criticità della soluzione adottata. >> MODEL, transcription, inaccurate >> PROTOTIPO
