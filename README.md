```cs

using System.Collections;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using UnityEditor;
using UnityEngine;

public class AnimationClipGenerator 
{
    [MenuItem("Assets/Automation/GenerateAnimationClip")]
    public static void AnimationGenFromTexture2D()
    {
        if (Selection.objects == null || Selection.objects.Length < 1)
        {
            return;
        }
        else
        {
            foreach(var s in Selection.objects)
            {
                AnimationGenFromTexture2D(AssetDatabase.GetAssetPath(s));
            }
        }

    } 
    public static void AnimationGenFromTexture2D(string path)
    { 
        string spriteSheet = path;
        var directoryName = System.IO.Path.GetDirectoryName(path);
        var fileName = System.IO.Path.GetFileName(path);
        var fileNameWithoutExtention = System.IO.Path.GetFileNameWithoutExtension(path);

        var savePath = directoryName+"/Animations/";
        if (!Directory.Exists(savePath)) { Directory.CreateDirectory(savePath); }

        var clip = new AnimationClip();
            clip.name = fileNameWithoutExtention;

        clip.frameRate = 12;

 

     
        Sprite[] sprites = AssetDatabase.LoadAllAssetsAtPath( spriteSheet ).OfType<Sprite>().ToArray();


        EditorCurveBinding spriteBinding = new EditorCurveBinding();
        spriteBinding.type = typeof(SpriteRenderer);
        spriteBinding.path = "";
        spriteBinding.propertyName = "m_Sprite"; 
        ObjectReferenceKeyframe[] spriteKeyFrames = new ObjectReferenceKeyframe[sprites.Length];
        for (int i = 0; i < (sprites.Length); i++)
        {
            spriteKeyFrames[i] = new ObjectReferenceKeyframe();
            spriteKeyFrames[i].time = 0.1f * i;
            spriteKeyFrames[i].value = sprites[i];
        }

        AnimationUtility.SetObjectReferenceCurve(clip, spriteBinding, spriteKeyFrames);


        AssetDatabase.CreateAsset(clip, savePath + fileNameWithoutExtention +".anim");
        AssetDatabase.Refresh(); 



        AnimationClipSettings tSettings = AnimationUtility.GetAnimationClipSettings(clip);
        tSettings.loopTime = true;
        AnimationUtility.SetAnimationClipSettings(clip, tSettings);
        EditorUtility.SetDirty(clip);
        AssetDatabase.SaveAssets();

      
    }
}


```
