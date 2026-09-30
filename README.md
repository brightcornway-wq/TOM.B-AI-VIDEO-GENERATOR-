"use client";
import { useState, useEffect } from "react";
const OWNER = "brightcornway@gmail.com";
export default function Home(){
 const [prompt,setPrompt]=useState("");
 const [email,setEmail]=useState("");
 const [duration,setDuration]=useState(10);
 const [video,setVideo]=useState("");
 const [loading,setLoading]=useState(false);
 const [left,setLeft]=useState(3);
 useEffect(()=>{
  const today=new Date().toDateString();
  const last=localStorage.getItem("lastDate");
  if(last!==today){localStorage.setItem("freeCount","0");localStorage.setItem("lastDate",today);}
  setLeft(3-parseInt(localStorage.getItem("freeCount")||"0"));
 },[]);
 const isOwner=email.toLowerCase()===OWNER.toLowerCase();
 const canGenerate=isOwner||left>0;
 async function generate(){
  if(!prompt)return alert("Write video idea");
  if(!canGenerate)return alert("Finish 3 free today");
  setLoading(true);setVideo("");
  try{
   const res=await fetch("/api/generate",{method:"POST",body:JSON.stringify({prompt,duration})});
   const data=await res.json();
   if(data.video){setVideo(data.video);
    if(!isOwner){const c=parseInt(localStorage.getItem("freeCount")||"0")+1;localStorage.setItem("freeCount",c.toString());setLeft(3-c);}
   }else alert("Check API key");
  }catch(e){alert("Error");}
  setLoading(false);
 }
 return (<div style={{maxWidth:500,margin:"0 auto",padding:20,fontFamily:"sans-serif"}}>
  <h1 style={{color:"green"}}>Naija Video AI 🎬</h1>
  <p>{isOwner?"Boss Unlimited ✅":`${left}/3 free left`}</p>
  <input placeholder="email e.g brightcornway@gmail.com" value={email} onChange={e=>setEmail(e.target.value)} style={{width:"100%",padding:12,marginBottom:10}}/>
  <textarea placeholder="Lagos danfo dancing..." value={prompt} onChange={e=>setPrompt(e.target.value)} style={{width:"100%",height:100,padding:12}}/>
  <select value={duration} onChange={e=>setDuration(e.target.value)} style={{width:"100%",padding:12,marginTop:10}}>
   <option value="5">10s</option><option value="30">30s</option><option value="60">1 min</option><option value="180">3 mins</option>
  </select>
  <button onClick={generate} disabled={loading} style={{width:"100%",padding:15,background:"green",color:"white",marginTop:15,border:"none",fontSize:18}}>{loading?"Generating 1-3 mins...":"Generate"}</button>
  {video&&<div style={{marginTop:20}}><video src={video} controls style={{width:"100%"}}/><a href={video} download style={{display:"block",marginTop:10,background:"black",color:"white",padding:10,textAlign:"center"}}>Download</a></div>}
 </div>);
}
