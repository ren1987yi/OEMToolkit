#region Using directives
using System;
using UAManagedCore;
using OpcUa = UAManagedCore.OpcUa;
using FTOptix.HMIProject;
using FTOptix.NetLogic;
using FTOptix.UI;
using FTOptix.NativeUI;
using FTOptix.CoreBase;
using FTOptix.Recipe;
using FTOptix.DataLogger;
using FTOptix.SQLiteStore;
using FTOptix.Store;
using FTOptix.RAEtherNetIP;
using FTOptix.Retentivity;
using FTOptix.CommunicationDriver;
using FTOptix.Alarm;
using FTOptix.Core;
using FTOptix.OPCUAServer;
#endregion

using System.Collections;
using System.Collections.Generic;
using System.Linq;
using System.IO;
using System.ComponentModel;
using Newtonsoft.Json;
using System.Text.RegularExpressions;


namespace OptixHelper.Utils;
public class StoreHelpr{
	public static IEnumerable<Dictionary<string,object>> Query(Store store,string sql){
		Object[,] ResultSet;
		String[] Header;
		
		store.Query(sql,out Header,out ResultSet);

		var result = new List<Dictionary<string,object>>();

		if(ResultSet.GetLength(0) > 0){
			for (int i = 0; i < ResultSet.GetLength(0); i++) {
				var dc = new Dictionary<string,object>();
				result.Add(dc);
				for(int j=0;j<ResultSet.GetLength(1);j++){

					try{

						var key = Header[j];
						var value = ResultSet[i,j];
						dc.Add(key,value);

					}catch{
						Log.Verbose1("Favorites","load error");
						//ClearFavorites();
					}
				}



			}
			

		}else{
			Log.Info("StoreHelper","query result empty");
			//ClearFavorites();
			
		}
		return result;

	}


	public static void InsertOneRow(Store store,string tablename,string[] names,object[] values){
		object[,] vals = new object[1,values.Length];
		for(var i = 0;i<values.Length;i++){
			vals[0,i] = values[i];

		}


		store.Insert(tablename,names,vals);
	}

	public static void Insert(Store store,string tablename,string[] names,object[,] values){
		try{

			store.Insert(tablename,names,values);
		}catch(Exception ex){
			throw new Exception(ex.Message);
		}
	}

	public static void ExecuteSql(Store store,string sql){
		Object[,] ResultSet;
		String[] Header;
		
		store.Query(sql,out Header,out ResultSet);

		//var result = new List<Dictionary<string,object>>();

		// if(ResultSet.GetLength(0) > 0){
		//     for (int i = 0; i < ResultSet.GetLength(0); i++) {
		// 		//var dc = new Dictionary<string,object>();
		// 		//result.Add(dc);
		// 		for(int j=0;j<ResultSet.GetLength(1);j++){

		// 			try{

		// 				var key = Header[j];
		// 				var value = ResultSet[i,j];
		// 				dc.Add(key,value);

		// 			}catch{
		// 				Log.Verbose1("Favorites","load error");
		// 				//ClearFavorites();
		// 			}
		// 		}



		//     }
			

		// }else{
		//     Log.Info("StoreHelper","execute result empty");
		//     //ClearFavorites();
			
		// }
		//return result;

	}
}


public class ObjectHelper{
	public static void DeepClone(IUAObject obj,IUAObject root){
		//var root = InformationModel.MakeObject(obj.BrowseName);
		foreach(var item in obj.Children){
			if(item.GetType() == typeof(UAVariable)){
				var v = InformationModel.MakeVariable(item.BrowseName,(item as IUAVariable).DataType);
				v.Value = (item as IUAVariable)?.Value;
				root.Add(v);
			}else if(item.GetType() == typeof(UAObject)){
				var o = InformationModel.MakeObject(item.BrowseName);
				DeepClone(item as IUAObject,o);
				root.Add(o);
			}else{
				throw new Exception("不知道啥类型了");
			}
		}
		//return root;
	}

	public static void DeepCopy(IUAObject obj,IUAObject root){
		//var root = InformationModel.MakeObject(obj.BrowseName);
		foreach(var item in obj.Children){
			if(item.GetType() == typeof(UAVariable)){
				var v = root.GetVariable(item.BrowseName);
				if(v != null){
					v.Value = (item as IUAVariable)?.Value;

				}
				
			}else if(item.GetType() == typeof(UAObject)){
				var node = root.GetObject(item.BrowseName);
				if(node != null){
					DeepCopy(item as IUAObject,node);

				}
				
			}else{
				throw new Exception("不知道啥类型了");
			}
		}
		//return root;
	}


	public static T GetVariabeleValue<T>(IUANode obj, string browsepath){
		var v = obj.GetVariable(browsepath);
		if(v == null){
			return default(T);
		}else{
			return (T)v.Value.Value;
		}

	}


	public static void SetVariableValue<T>(IUANode obj,string browsepath,object value){
		var v = obj.GetVariable(browsepath);
		if(v == null){
			try{

				v.Value = new UAValue(value);
			}catch(Exception ex){
				throw new Exception(ex.Message);
			}
		}
	}


}


public class JsonHelper{
		public static string? Serialize(object value)
	{
		if(value != null)
		{
			try
			{

				var content = JsonConvert.SerializeObject(value, Formatting.Indented, new JsonSerializerSettings()
				{
					TypeNameHandling = TypeNameHandling.All,
					TypeNameAssemblyFormatHandling = TypeNameAssemblyFormatHandling.Full
				});
				return content;
			}
			catch(Exception err)
			{
				return null;
			}
		}
		return null;
	}

	public static T Deserialize<T>(string jsoncontent)
	{

		try
		{
			var obj = JsonConvert.DeserializeObject<T>(jsoncontent, new JsonSerializerSettings()
			{
				TypeNameHandling = TypeNameHandling.All,
				TypeNameAssemblyFormatHandling = TypeNameAssemblyFormatHandling.Full

			});
			return obj;
		}
		catch
		{
			return default(T);
		}

	
	}
}

public class BinaryHelper
{
	public static bool? GetBit(int data, int bit)
	{
		if (bit > 31 || bit < 0)
		{
			return null;
		}
		return ((data >> bit) & 1) == 1; //将要取值得位右移到第0位并将左侧第0位以外的位全部置0
	}

	public static void SetBit(ref int data, int bit)
	{
		if (bit > 31 || bit < 0)
		{
			return;
		}

		data |= 0x1 << bit;
	}

	public static void PutBit(ref int data, bool value, int bit)
	{
		if (value)
		{
			SetBit(ref data, bit);
		}
		else
		{
			ClrBit(ref data, bit);
		}
	}

	public static void ClrBit(ref int data, int bit)
	{

		if (bit > 31 || bit < 0)
		{
			return;
		}

		data &= ~(0x1 << bit);
	}

	public static void ToggleBit(ref int data, int bit)
	{
		if (bit > 31 || bit < 0)
		{
			return;
		}

		data ^= 0x1 << bit;


	}
}

public class RegexHelper
{

	//帐号是否合法(字母开头，允许5-16字节，允许字母数字下划线)：^[a-zA-Z][a-zA-Z0-9_]{4,15}$
	public static bool CheckTagNameRule(string input)
	{
		const string RULE_TAGNAME = @"^[a-zA-Z][a-zA-Z0-9]{0,9}$";
		Regex rx = new Regex(RULE_TAGNAME,
					RegexOptions.Compiled | RegexOptions.IgnoreCase);

		Match match = rx.Match(input);
		
		return match.Success;
	}
}

	public class EnumerableHelper
{

	
	/// <summary>
	/// 去重
	/// </summary>
	/// <typeparam name="T"></typeparam>
	/// <param name="ListA"></param>
	/// <returns></returns>
	public static IEnumerable<T> Distinct<T>( IEnumerable<T> ListA)
	{
		return ListA.Distinct().ToList();//去重

	}

	/// <summary>
	/// 差集
	/// </summary>
	/// <typeparam name="T"></typeparam>
	/// <param name="ListA"></param>
	/// <param name="ListB"></param>
	/// <returns></returns>
	public static IEnumerable<T> Except<T>(IEnumerable<T> ListA ,IEnumerable<T> ListB)
	{
		return ListA.Except(ListB).ToList();//差集

	}


	/// <summary>
	/// 并集
	/// </summary>
	/// <typeparam name="T"></typeparam>
	/// <param name="ListA"></param>
	/// <param name="ListB"></param>
	/// <returns></returns>
	public static IEnumerable<T> Union<T>(IEnumerable<T> ListA, IEnumerable<T> ListB)
	{
		return ListA.Union(ListB).ToList();  //并集

	}

	/// <summary>
	/// 交集
	/// </summary>
	/// <typeparam name="T"></typeparam>
	/// <param name="ListA"></param>
	/// <param name="ListB"></param>
	/// <returns></returns>
	public static IEnumerable<T> Intersect<T>(IEnumerable<T> ListA, IEnumerable<T> ListB)
	{
		return ListA.Intersect(ListB).ToList();  //交集

	}



	public static IEnumerable<T> Different<T>(IEnumerable<T> ListA, IEnumerable<T> ListB)
	{
		var sameArr = ListA.Intersect(ListB).ToArray();//找出相同元素(即交集)
		return ListA.Where(c => !ListB.Contains(c)).ToArray();//找出不同的元素(即交集的补集)
	}


}


